채팅 데이터 형식
메타 파일

이 문서에서는 대화 데이터의 구성 형식에 대해 자세히 설명합니다. 현재 모든 데이터 세트의 메타 정보를 관리하기 위해 JSON 파일을 사용합니다. 형식은 다음과 같습니다.

{
  "your-custom-dataset-1": {
    "root": "이미지/경로/",
    "annotation": "jsonl/주석/경로",
    "data_augment": false,
    "repeat_time": 1,
    "length": "데이터 세트의 샘플 수"
  },
  ...
}
content_copy
download
Use code with caution.

여기서 root는 데이터 세트의 루트 디렉토리, annotation은 주석 파일의 경로, data_augment는 데이터 증강이 필요한지 여부, repeat_time은 데이터 세트가 반복되는 횟수, length는 데이터 세트의 샘플 수입니다.

예를 들어, ShareGPT4V 데이터 세트의 파일은 다음과 같습니다.

{
  "sharegpt4v_instruct_gpt4-vision_cap100k": {
    "root": "playground/data/",
    "annotation": "playground/opensource/sharegpt4v_instruct_gpt4-vision_cap100k.jsonl",
    "data_augment": false,
    "repeat_time": 1,
    "length": 102025
  },
  ...
}
content_copy
download
Use code with caution.

이 파일과 유사하게 이 JSON 파일에 여러 데이터 세트를 추가할 수 있습니다.

현재 순수 텍스트 데이터, 단일 이미지 데이터, 다중 이미지(인터리브) 데이터 및 비디오 데이터의 네 가지 유형의 데이터 세트를 지원합니다. JSONL 파일의 모든 항목이 동일한 유형일 필요는 없습니다. 즉, JSONL 파일에 다른 유형의 데이터가 포함될 수 있습니다.

순수 텍스트 데이터

순수 텍스트 데이터의 경우 JSONL 파일을 사용하여 데이터를 저장합니다. 각 항목은 다음과 같은 형식으로 구성된 사전입니다. 순수 텍스트 데이터의 항목에는 image 필드가 포함되어서는 안 됩니다.

{
  "id": 0,
  "conversations": [
    {"from": "human", "value": "사용자 입력"},
    {"from": "gpt", "value": "어시스턴트 출력"},
    {"from": "human", "value": "사용자 입력"},
    {"from": "gpt", "value": "어시스턴트 출력"}
  ]
}
content_copy
download
Use code with caution.
Json

여기서 id는 데이터의 고유 식별자이고 conversations는 여러 대화를 포함하는 목록입니다. 각 대화는 from과 value의 두 가지 키-값 쌍이 있는 사전입니다. from은 화자의 신원을 나타내고 value는 화자의 입력을 나타냅니다.

이 데이터 형식은 LLaVA 1.5와 일치하지만 LLaVA 1.5는 JSON을 사용하는 반면 우리는 JSONL을 사용한다는 차이점이 있습니다.

순수 텍스트 데이터의 예:

{"id": 0, "conversations": [{"from": "human", "value": "두 변이 주어졌을 때 빗변의 길이를 구하세요.\n\"side_length1 = 5, side_length2 = 6\""}, {"from": "gpt", "value": "직각 삼각형의 두 변이 주어졌을 때 빗변의 길이를 구하려면 피타고라스 정리를 사용할 수 있습니다. 이 정리는 빗변 길이의 제곱이 다른 두 변의 길이의 제곱의 합과 같다는 것입니다.\n\n이 경우 한 변의 길이는 5이고 다른 변의 길이는 6입니다. 이 값을 공식에 대입하면 다음과 같습니다.\n\n빗변^2 = 5^2 + 6^2\n                 = 25 + 36\n                 = 61\n\n빗변의 길이를 구하려면 61의 제곱근을 구합니다.\n\n빗변 = √61\n              = 7.81 (소수점 이하 두 자리로 반올림)\n\n따라서 빗변의 길이는 약 7.81단위입니다."}]}
content_copy
download
Use code with caution.
Json

더 읽기 쉬운 버전은 다음과 같습니다.

{
  "id": 0,
  "conversations": [
    {
      "from": "human",
      "value": "두 변이 주어졌을 때 빗변의 길이를 구하세요.\n\"side_length1 = 5, side_length2 = 6\""
    },
    {
      "from": "gpt",
      "value": "직각 삼각형의 두 변이 주어졌을 때 빗변의 길이를 구하려면 피타고라스 정리를 사용할 수 있습니다. 이 정리는 빗변 길이의 제곱이 다른 두 변의 길이의 제곱의 합과 같다는 것입니다.\n\n이 경우 한 변의 길이는 5이고 다른 변의 길이는 6입니다. 이 값을 공식에 대입하면 다음과 같습니다.\n\n빗변^2 = 5^2 + 6^2\n               = 25 + 36\n               = 61\n\n빗변의 길이를 구하려면 61의 제곱근을 구합니다.\n\n빗변 = √61\n            = 7.81 (소수점 이하 두 자리로 반올림)\n\n따라서 빗변의 길이는 약 7.81단위입니다."
    }
  ]
}
content_copy
download
Use code with caution.
Json
단일 이미지 데이터

단일 이미지 데이터의 경우 JSONL 파일을 사용하여 데이터를 저장합니다. 각 항목은 다음과 같은 형식으로 구성된 사전입니다. 단일 이미지 데이터의 각 항목에는 문자열인 image 필드가 포함되어야 합니다.

image 필드의 경로는 root 필드를 기준으로 합니다. root 필드와 image 필드를 연결하면 이미지의 전체 경로가 됩니다. 나중에 사용할 수 있도록 각 데이터 샘플에 width 및 height 정보를 포함하는 것이 좋습니다.

{
  "id": 0,
  "image": "이미지/경로.jpg",
  "width": 111,
  "height": 222,
  "conversations": [
    {"from": "human", "value": "<image>\n사용자 입력"},
    {"from": "gpt", "value": "어시스턴트 출력"},
    {"from": "human", "value": "사용자 입력"},
    {"from": "gpt", "value": "어시스턴트 출력"}
  ]
}
content_copy
download
Use code with caution.
Json

여기서 <image>는 이미지가 삽입된 위치를 나타내며 <image> 자리 표시자의 수는 이미지 수와 일치해야 합니다. 단일 이미지 데이터에서 <image> 자리 표시자는 모든 대화에서 한 번만 나타나야 합니다.

단일 이미지 데이터의 예:

{"id": 0, "image": "images/00000000.jpg", "conversations": [{"from": "human", "value": "<image>\n이미지에서 읽을 수 있는 텍스트를 추출할 수 있습니까?"}, {"from": "gpt", "value": "Dares Wins Vol. 5 Tommy's Heroes Vol. 6: For Tomorrow Vol. 7: Closing Time 미니시리즈. 클라크 켄트가 슈퍼맨과 악명 높은 살인자 토미 모나한의 관계에 대해 인터뷰를 하고 있습니다. 대화를 나누고..."}], "width": 897, "height": 1152}
content_copy
download
Use code with caution.
Json

더 읽기 쉬운 버전은 다음과 같습니다.

{
  "id": 0,
  "image": "images/00000000.jpg",
  "width": 897,
  "height": 1152,
  "conversations": [
    {
      "from": "human",
      "value": "<image>\n이미지에서 읽을 수 있는 텍스트를 추출할 수 있습니까?"
    },
    {
      "from": "gpt",
      "value": "Dares Wins Vol. 5 Tommy's Heroes Vol. 6: For Tomorrow Vol. 7: Closing Time 미니시리즈. 클라크 켄트가 슈퍼맨과 악명 높은 살인자 토미 모나한의 관계에 대해 인터뷰를 하고 있습니다. 대화를 나누고..."
    }
  ]
}
content_copy
download
Use code with caution.
Json
접지 / 감지 데이터

접지 또는 감지 데이터의 경우 이미지의 너비와 높이를 사용하여 각 좌표 상자를 [0, 1000] 범위로 정규화하고 정규화된 좌표를 정수로 반올림합니다.

def normalize_coordinates(box, image_width, image_height):
    x1, y1, x2, y2 = box
    normalized_box = [
        round((x1 / image_width) * 1000),
        round((y1 / image_height) * 1000),
        round((x2 / image_width) * 1000),
        round((y2 / image_height) * 1000)
    ]
    return normalized_box
content_copy
download
Use code with caution.
Python

모델의 응답을 <ref>클래스 이름</ref><box>[[x1, y1, x2, y2], ...]</box> 형식으로 구성합니다.

RefCOCO 데이터 세트의 예:

{"id": 2324, "image": "COCO_train2014_000000581857.jpg", "width": 427, "height": 640, "conversations": [{"from": "human", "value": "<image>\n이 문장이 설명하는 영역의 경계 상자 좌표를 제공하십시오: <ref>파란색 셔츠를 입은 여성</ref>"}, {"from": "gpt", "value": "<ref>파란색 셔츠를 입은 여성</ref><box>[[243, 469, 558, 746]]</box>"}, {"from": "human", "value": "이 문장이 설명하는 영역의 경계 상자 좌표를 제공하십시오: <ref>오른쪽에서 카메라를 향한 회색 셔츠를 입은 여성</ref>"}, {"from": "gpt", "value": "<ref>오른쪽에서 카메라를 향한 회색 셔츠를 입은 여성</ref><box>[[507, 409, 698, 740]]</box>"}, {"from": "human", "value": "이 문장이 설명하는 영역의 경계 상자 좌표를 제공하십시오: <ref>안경을 쓴 여성이 야외 과일 시장에서 쇼핑을 하고 있습니다</ref>"}, {"from": "gpt", "value": "<ref>안경을 쓴 여성이 야외 과일 시장에서 쇼핑을 하고 있습니다</ref><box>[[196, 422, 395, 708]]</box>"}]}
content_copy
download
Use code with caution.
Json

더 읽기 쉬운 버전은 다음과 같습니다.

{
  "id": 2324,
  "image": "COCO_train2014_000000581857.jpg",
  "width": 427,
  "height": 640,
  "conversations": [
    {
      "from": "human",
      "value": "<image>\n이 문장이 설명하는 영역의 경계 상자 좌표를 제공하십시오: <ref>파란색 셔츠를 입은 여성</ref>"
    },
    {
      "from": "gpt",
      "value": "<ref>파란색 셔츠를 입은 여성</ref><box>[[243, 469, 558, 746]]</box>"
    },
    {
      "from": "human",
      "value": "이 문장이 설명하는 영역의 경계 상자 좌표를 제공하십시오: <ref>오른쪽에서 카메라를 향한 회색 셔츠를 입은 여성</ref>"
    },
    {
      "from": "gpt",
      "value": "<ref>오른쪽에서 카메라를 향한 회색 셔츠를 입은 여성</ref><box>[[507, 409, 698, 740]]</box>"
    },
    {
      "from": "human",
      "value": "이 문장이 설명하는 영역의 경계 상자 좌표를 제공하십시오: <ref>안경을 쓴 여성이 야외 과일 시장에서 쇼핑을 하고 있습니다</ref>"
    },
    {
      "from": "gpt",
      "value": "<ref>안경을 쓴 여성이 야외 과일 시장에서 쇼핑을 하고 있습니다</ref><box>[[196, 422, 395, 708]]</box>"
    }
  ]
}
content_copy
download
Use code with caution.
Json

V3Det 데이터 세트의 예:

{"id": 78281, "image": "images/x00001541/000106464.jpg", "width": 800, "height": 800, "conversations": [{"from": "human", "value": "<image>\n다음 이미지의 모든 객체를 감지하고 레이블을 지정하고 위치를 표시하십시오."}, {"from": "gpt", "value": "물론 이미지의 모든 객체를 감지하고 레이블을 지정하고 위치를 표시하겠습니다.\n\n```\n<ref>방탄 헬멧</ref><box>[[1, 2, 269, 235]]</box>\n<ref>방탄 의류</ref><box>[[650, 619, 990, 993]]</box>\n<ref>총 모델</ref><box>[[32, 231, 977, 662]]</box>\n<ref>나사</ref><box>[[754, 376, 851, 429]]</box>\n<ref>수갑</ref><box>[[698, 228, 931, 386]]</box>\n<ref>95식 돌격 소총</ref><box>[[39, 229, 983, 667]]</box>\n<ref>방패</ref><box>[[30, 597, 273, 993]]</box>\n<ref>망원경</ref><box>[[666, 38, 890, 217]]</box>\n<ref>무선 워키토키</ref><box>[[295, 2, 370, 226], [374, 0, 447, 226]]</box>\n<ref>폭탄</ref><box>[[473, 61, 552, 181], [569, 61, 648, 183]]</box>\n<ref>무기</ref><box>[[302, 617, 342, 993]]</box>\n<ref>용기</ref><box>[[355, 653, 644, 991]]</box>\n<ref>유물</ref><box>[[915, 0, 981, 294]]</box>\n```\n"}]}
content_copy
download
Use code with caution.
Json

더 읽기 쉬운 버전은 다음과 같습니다.

{
  "id": 78281,
  "image": "images/x00001541/000106464.jpg",
  "width": 800,
  "height": 800,
  "conversations": [
    {
      "from": "human",
      "value": "<image>\n다음 이미지의 모든 객체를 감지하고 레이블을 지정하고 위치를 표시하십시오."
    },
    {
      "from": "gpt",
      "value": "물론 이미지의 모든 객체를 감지하고 레이블을 지정하고 위치를 표시하겠습니다.\n\n<ref>방탄 헬멧</ref><box>[[1, 2, 269, 235]]</box>\n<ref>방탄 의류</ref><box>[[650, 619, 990, 993]]</box>\n<ref>총 모델</ref><box>[[32, 231, 977, 662]]</box>\n<ref>나사</ref><box>[[754, 376, 851, 429]]</box>\n<ref>수갑</ref><box>[[698, 228, 931, 386]]</box>\n<ref>95식 돌격 소총</ref><box>[[39, 229, 983, 667]]</box>\n<ref>방패</ref><box>[[30, 597, 273, 993]]</box>\n<ref>망원경</ref><box>[[666, 38, 890, 217]]</box>\n<ref>무선 워키토키</ref><box>[[295, 2, 370, 226], [374, 0, 447, 226]]</box>\n<ref>폭탄</ref><box>[[473, 61, 552, 181], [569, 61, 648, 183]]</box>\n<ref>무기</ref><box>[[302, 617, 342, 993]]</box>\n<ref>용기</ref><box>[[355, 653, 644, 991]]</box>\n<ref>유물</ref><box>[[915, 0, 981, 294]]</box>\n"
    }
  ]
}
content_copy
download
Use code with caution.
Json
다중 이미지 데이터

다중 이미지 데이터의 경우 JSONL 파일을 사용하여 데이터를 저장합니다. 각 항목은 다음과 같은 형식으로 구성된 사전입니다. 다중 이미지 데이터의 각 항목에는 문자열 목록인 image 필드가 포함되어야 합니다.

목록의 각 요소는 root 필드를 기준으로 하는 경로입니다. root 필드와 각 요소를 연결하면 이미지의 전체 경로가 됩니다. 나중에 사용할 수 있도록 각 데이터 샘플에 width_list 및 height_list 정보를 포함하는 것이 좋습니다.

{
  "id": 0,
  "image": ["이미지1/경로.jpg", "이미지2/경로.jpg", "이미지3/경로.jpg"],
  "width_list": [111, 222, 333],
  "height_list": [111, 222, 333],
  "conversations": [
    {"from": "human", "value": "<image>\n사용자 입력 <image>\n사용자 입력"},
    {"from": "gpt", "value": "어시스턴트 출력"},
    {"from": "human", "value": "<image>\n사용자 입력"},
    {"from": "gpt", "value": "어시스턴트 출력"}
  ]
}
content_copy
download
Use code with caution.
Json

여기서 <image>는 이미지가 삽입된 위치를 나타내며 <image> 자리 표시자의 수는 이미지 수와 일치해야 합니다.
이 예에서 image 필드 목록에는 세 개의 요소가 포함되어 있으므로 <image> 자리 표시자도 세 번 나타나야 합니다.

다중 이미지 데이터의 예:

{"id": 0, "image": ["cimages/multimages/16/5pc.png", "cimages/multimages/16/5pd.png", "cimages/multimages/16/1602207874_p5b.png", "cimages/multimages/16/5pe.png", "cimages/multimages/16/1473016381_p5a.png"], "height_list": [23, 22, 23, 41, 52], "width_list": [240, 240, 240, 240, 240], "conversations": [{"from": "human", "value": "F = {2, 5, 7, 9}라고 가정합니다.\n\nG = {1, 4, 6, 8}이라고 가정합니다.\n\n다음 중 참인 것은 무엇입니까?\nA. \n<image>\n\nB. /\n<image>\n\nC. /\n<image>\n\nD. /\n<image>\n\nE. /\n<image>\n\n주어진 선택 사항의 문자로 직접 답변하십시오."}, {"from": "gpt", "value": "A"}]}
content_copy
download
Use code with caution.
Json

더 읽기 쉬운 버전은 다음과 같습니다.

{
  "id": 0,
  "image": [
    "cimages/multimages/16/5pc.png",
    "cimages/multimages/16/5pd.png",
    "cimages/multimages/16/1602207874_p5b.p
content_copy
download
Use code with caution.
Json
