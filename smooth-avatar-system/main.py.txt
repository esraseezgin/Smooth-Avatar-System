import cv2
import mediapipe as mp

cap = cv2.VideoCapture(0, cv2.CAP_DSHOW)

W, H = 640, 480

mp_pose = mp.solutions.pose
pose = mp_pose.Pose(min_detection_confidence=0.7)

mp_hands = mp.solutions.hands
hands = mp_hands.Hands(max_num_hands=2, min_detection_confidence=0.7)

prev_points = {}

def smooth(name, x, y, alpha=0.7):
    if name not in prev_points:
        prev_points[name] = (x, y)
        return x, y

    px, py = prev_points[name]

    nx = int(x * alpha + px * (1 - alpha))
    ny = int(y * alpha + py * (1 - alpha))

    prev_points[name] = (nx, ny)
    return nx, ny


while True:
    ret, frame = cap.read()
    if not ret:
        break

    frame = cv2.flip(frame, 1)
    frame = cv2.resize(frame, (W, H))

    rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)

    pose_res = pose.process(rgb)
    hand_res = hands.process(rgb)

    canvas = frame.copy()
    canvas[:] = (20, 20, 20)

    # BODY

    if pose_res.pose_landmarks:
        lm = pose_res.pose_landmarks.landmark

        def p(i):
            x = int(lm[i].x * W)
            y = int(lm[i].y * H)
            return x, y

        ls = smooth("ls", *p(11))
        rs = smooth("rs", *p(12))
        le = smooth("le", *p(13))
        re = smooth("re", *p(14))
        lw = smooth("lw", *p(15))
        rw = smooth("rw", *p(16))

        # head
        head_x = (ls[0] + rs[0]) // 2
        head_y = ls[1] - 50
        head_x, head_y = smooth("head", head_x, head_y)

        cv2.circle(canvas, (head_x, head_y), 28, (255, 255, 255), -1)

        # torso
        hip_y = ls[1] + 120
        cv2.line(canvas, ls, (ls[0], hip_y), (255, 255, 255), 5)
        cv2.line(canvas, rs, (rs[0], hip_y), (255, 255, 255), 5)

        # arms
        cv2.line(canvas, ls, le, (0, 255, 0), 6)
        cv2.line(canvas, le, lw, (0, 255, 0), 6)

        cv2.line(canvas, rs, re, (0, 255, 0), 6)
        cv2.line(canvas, re, rw, (0, 255, 0), 6)

        # joints
        for pt in [ls, rs, le, re, lw, rw]:
            cv2.circle(canvas, pt, 8, (0, 255, 255), -1)

    # HANDS

    if hand_res.multi_hand_landmarks:
        for hand in hand_res.multi_hand_landmarks:
            for i in range(21):
                x = int(hand.landmark[i].x * W)
                y = int(hand.landmark[i].y * H)

                x, y = smooth(f"hand_{i}", x, y)

                cv2.circle(canvas, (x, y), 5, (0, 0, 255), -1)

    # SIDE BY SIDE

    out = cv2.hconcat([frame, canvas])

    cv2.imshow("Smooth Avatar System", out)

    if cv2.waitKey(1) & 0xFF == ord("q"):
        break

cap.release()
cv2.destroyAllWindows()
