Open(RVisualMesh.cpp) <br>
Hex color picker: [Hex colours](https://htmlcolorcodes.com/color-picker/) <br>
Find <br>

    void RVisualMesh::GetEnChantColor(DWORD* color)
    {
      if(!color) return;

      if(m_EnchantType==REnchantType_Fire) {
        color[0] = 0x4fff6666;
        color[1] = 0x0fff6666;
      }
      else if(m_EnchantType==REnchantType_Cold) {
        color[0] = 0x4f6666ff;
        color[1] = 0x0f6666ff;
      }
      else if(m_EnchantType==REnchantType_Lightning) {
        color[0] = 0x4f66ffff;
        color[1] = 0x0f66ffff;
      }
      else if(m_EnchantType==REnchantType_Poison) {
        color[0] = 0x4f66ff66;
        color[1] = 0x0f66ff66;
      }
      else {
        color[0] = 0x4fffffff;
        color[1] = 0x0fffffff;
      }
    }

Replace <br>

    else {
        color[0] = 0x4f[hex]; //replace the [hex] with the color hex code you chose
        color[1] = 0x0f[hex]; //same here !!
      }


Rainbow colour trail <br>

        int i = (timeGetTime() / 300) % 8;
        unsigned int colorCodes[] = {
            0x7f0055aa, // Hồng
            0x7fff5500, // Cam
            0x7f5500ff, // Xanh Nước Biển
            0x7f55ff00, // Xanh Lime
            0x7f5500ff, // Tím
            0x7f5500ff,
            0x7f550000, // Đỏ
            0x7f0055ff, // Xanh Dương
            0x7fffffff // Trắng
        };

        color[0] = colorCodes[i];
        color[1] = colorCodes[i];





