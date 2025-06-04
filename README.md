# SYW2Plus

임진록2+에서 사용되는 SPR, YTL, YAV 파일 포맷에 대한 연구 및 분석 내용입니다. 공식적인 자료가 없기 때문에 일부 내용은 올바르지 않을 수 있습니다.

## SPR
스프라이트(SPR, Sprite) 이미지 파일로, 각 프레임은 픽셀별 색상이 아닌 팔레트 인덱스를 기반으로 하고 있습니다. 즉, 8비트 비트맵과 유사합니다.

자체 제작 파일 구조를 따르는 것으로 보이기 때문에, 이와 비슷한 구조를 갖는 이미지 파일을 발견하지 못했습니다.

### 구조
```cpp
struct SPR {
  uint32_t Signature;                 // 시그니쳐
  uint32_t FrameWidth;                // 한 프레임의 가로 길이
  uint32_t FrameHeight;               // 한 프레임의 세로 길이
  uint32_t NumberOfFrames;            // 총 프레임의 수
  uint8_t Dummy1[1200];               // 더미 배열 1
  uint32_t Offsets[300];              // 오프셋 기록 배열
  uint16_t CompressedSizes[300];      // 압축 기록 배열
  uint32_t TotalCompressedSize;       // 총 압축 크기
  uint32_t Width;                     // SPR 이미지의 가로 길이
  uint32_t Height;                    // SPR 이미지의 세로 길이
  uint8_t Dummy2[32];                 // 더미 배열 2
  uint8_t* CompressedPaletteIndexes;  // 팔레트 인덱스 배열
};
```

SYW2Plus에서 사용되는 SPR 파일의 `Signature`는 항상 0x09의 값을 가집니다. 실행 파일에서 0x09가 아니면 파일 읽기를 실패합니다.

> [!TIP]
> `Math.floor(Width / FrameWidth)`와 `Math.floor(Height / FrameHeight)` 식을 통해 X축과 Y축에 존재하는 프레임의 수를 취득할 수 있습니다. 이를 통헤 NxN으로 구성되었다는 걸 확인할 수 있습니다.

### 팔레트 인덱스 압축
팔레트 인덱스는 RLE 방식을 기반으로 압축되어 있습니다. 즉, SYW2Plus에선 0xFE를 투명 팔레트 인덱스로 사용하는데요, 이 값만 압축합니다.

[0xFE][0xFE의 수]로 압축되고, 0xFE 값을 만나면 다음 바이트를 읽어 그 수만큼 0xFE를 추가하면 됩니다. 나머지 인덱스 데이터는 압축되어있지 않습니다.

지금은 찾아보기 힘든 정보지만(?) 과거 '0번 압축'이라 불리었습니다. 0번 또는 255번을 투명으로 지정해 사용했기 때문에 그렇습니다.


## YTL
타일맵 이미지 파일로, Y는 임진록(Yimjinrok)의 줄임말로 추정됩니다.

SPR 파일 구조와 매우 유사한 구조를 갖고 있으며, 압축 방식만 조금 다릅니다.

SPR 파일과 마찬가지로, 자체 제작 파일 구조를 따르는 것으로 보이기 때문에, 이와 비슷한 구조를 갖는 이미지 파일을 발견하지 못했습니다.

### 구조
```cpp
struct YimjinrokTiLe {
  uint32_t Signature;                 // 시그니쳐
  uint32_t TileWidth;                 // 한 타일의 가로 길이
  uint32_t TileHeight;                // 한 타일의 세로 길이
  uint32_t NumberOfTiles;             // 총 프레임의 수
  uint8_t Dummy1[1200];               // 더미 배열 1
  uint32_t Offsets[300];              // 오프셋 기록 배열
  uint16_t CompressedSizes[300];      // 압축 기록 배열
  uint32_t TotalCompressedSize;       // 총 압축 크기
  uint32_t Width;                     // YTL 이미지의 가로 길이
  uint32_t Height;                    // YTL 이미지의 세로 길이
  uint8_t Dummy2[32];                 // 더미 배열 2
  uint8_t* CompressedPaletteIndexes;  // 팔레트 인덱스 배열
};
```

SYW2Plus에서 사용되는 YTL 파일의 `Signature`는 항상 0x09의 값을 가집니다. 실행 파일에서 0x09가 아니면 파일 읽기를 실패합니다.

압축 기록 배열은 사용되지 않는 걸로 보입니다.

> [!TIP]
> `Math.floor(Width / TileWidth)`와 `Math.floor(Height / TileHeight)` 식을 통해 X축과 Y축에 존재하는 프레임의 수를 취득할 수 있습니다. 이를 통헤 NxN으로 구성되었다는 걸 확인할 수 있습니다.

### 팔레트 인덱스 압축
기본적으로 YTL은 투명 배경으로 설정 후 처리하는 걸 기반으로 하는 것 같습니다. 그래서 다음과 같은 구조를 갖는 것으로 보입니다.

> [X축 시작 위치][다음에 등장하는 팔레트 인덱스의 수][팔레트 인덱스][팔레트 인덱스]...

예를 들어, 1F 02 F3 49라는 값이 있다면... 0x1F(31)만큼 X축을 이동한 후 0x02의 값만큼 팔레트 인덱스에 해당하는 색상을 찍어냅니다.

한 타일의 길이가 64라고 할 때, 64의 길이 모두 투명으로 채웁니다. 그리고 0x1F만큼 이동한 후 이 위치에서 부터 0xF3과 0x49에 해당하는 색상을 찍죠. 이후는 어차피 투명이라 따로 처리할 것도 없기 때문에 다음 Y축으로 이동합니다.
