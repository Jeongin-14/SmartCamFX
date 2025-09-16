# SmartCamFX
A simple video recorder with filters using OpenCV


## 프로젝트 이름 및 설명
#  SmartCamFX
SmartCamFX는 OpenCV를 활용한 실시간 웹캠 영상 미리보기 및 녹화 프로그램입니다.  
사용자는 Preview 모드와 Record 모드를 전환할 수 있으며, 밝기/대비 조절 및 좌우 반전 필터(Flip) 등의 영상 효과도 적용할 수 있습니다.

## 사용 기술
- Python 3.x
- OpenCV (cv2)
- VideoCapture / VideoWriter
- 키보드 이벤트 처리 (`cv.waitKey`)
- 이미지 처리: `convertScaleAbs`, `flip`

## 기능 설명
실시간 카메라 프리뷰: 웹캠으로부터 실시간 영상 표시
영상 녹화: `Record` 모드에서 `.avi` 형식으로 저장
모드 전환: `Space 키`로 Preview/Record 모드 전환
녹화 표시: 녹화 중 빨간 원 표시
필터 기능: 밝기/대비 조절, 좌우 반전(`F 키`)
종료: `ESC 키`로 프로그램 종료



## 개인적으로 개발한 추가 기능

- `convertScaleAbs()`를 이용한 밝기(Brightness) 및 대비(Contrast) 조절
- `cv.flip()`을 활용한 좌우 반전(Flip) 기능 (`F` 키로 토글)
- 사용자가 쉽게 수정할 수 있도록 코덱(FourCC), 해상도, FPS를 명시적으로 설정
- 키보드로 간단하게 제어할 수 있는 UI-less 조작 방식



## 실행 방법
import cv2 as cv

# 기본값 설정
cap = cv.VideoCapture(0)
fourcc = cv.VideoWriter_fourcc(*'XVID')
fps = 20.0
frame_size = (640, 480)
brightness = 30
contrast = 1.3
flip_mode = False

out = None
record = False

cap.set(cv.CAP_PROP_FRAME_WIDTH, frame_size[0])
cap.set(cv.CAP_PROP_FRAME_HEIGHT, frame_size[1])

while cap.isOpened():
    ret, frame = cap.read()
    if not ret:
        print("카메라 오류")
        break

    # 필터 적용: 밝기 및 대비 조절
    frame = cv.convertScaleAbs(frame, alpha=contrast, beta=brightness)

    # Flip 효과 (좌우 반전)
    if flip_mode:
        frame = cv.flip(frame, 1)

    # Record 모드일 경우 파일로 저장
    if record:
        if out is None:
            out = cv.VideoWriter('smartcamfx_output.avi', fourcc, fps, frame_size)
        out.write(frame)
        cv.circle(frame, (30, 30), 10, (0, 0, 255), -1)

    # 화면에 출력
    cv.imshow('SmartCamFX - Press [Space] to Record, [F] to Flip, [ESC] to Quit', frame)

    key = cv.waitKey(1) & 0xFF
    if key == 27:  # ESC
        break
    elif key == 32:  # Space
        record = not record
        if not record and out:
            out.release()
            out = None
    elif key == ord('f'):  # Flip 전환
        flip_mode = not flip_mode

cap.release()
if out:
    out.release()
cv.destroyAllWindows()



# 스크린샷 이미지




