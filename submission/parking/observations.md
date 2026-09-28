# Quan sát vạch ô đỗ

- Hai vạch `parking_line` đã vẽ (mô tả vị trí trong ảnh): export có 16 polyline `parking_line`, chia ba dãy ô.
  Hai vạch rõ nhất để đối chiếu:
  - **Dãy ô gần camera** (đáy ảnh): vạch trắng từ ~(407, 653) xuống mép dưới ~(529, 720) và vạch từ ~(696, 623) ra
    mép phải ~(960, 685). Hai vạch này là ranh giữa các ô liền nhau của dãy tiền cảnh; cùng dãy còn vạch sát mép trái
    ~(29, 681)→(24, 720) và đoạn ngắn ở mép phải ~(924, 597)→(960, 603). Các vạch chạm mép ảnh dừng tại mép, không kéo dài
    phần ngoài khung.
  - **Dãy ô giữa**: 5 vạch nghiêng song song, ví dụ ~(174, 522)→(247, 563) và ~(286, 520)→(421, 554), cộng vạch gần
    thẳng đứng bên trái ~(60, 523)→(49, 572). Mỗi vạch ngăn hai ô cạnh nhau, đầu dưới mở ra lối xe chạy.
  - **Dãy ô xa** (6 đoạn ngắn quanh y≈495–510, gần xe đỏ): xem mục ca chưa chắc bên dưới.
- Một vạch/dấu sơn hoặc biên **không** vẽ, và vì sao: đường sơn mảnh, dài chạy gần như ngang qua giữa ảnh
  (từ mép trái khoảng y≈540 lên dần tới khoảng (900, 512) ở bên phải, cắt qua các vạch nghiêng của dãy ô giữa). Đường
  này chạy **dọc theo cả dãy ô**, không nằm giữa hai ô cạnh nhau, nên nó là biên phân cách dãy ô / lối xe chạy chứ
  không phải ranh giới một ô đỗ riêng lẻ → không gán `parking_line`. Tôi cũng không dùng nó làm cạnh trên của
  `free_space`, vì bám theo nó sẽ kéo cả các ô của dãy giữa vào vùng lối xe chạy.
- Polygon `free_space` dừng ở đâu; có phần bị che nào không:
  - Polygon chính là **dải lối xe chạy giữa dãy ô gần camera và dãy ô giữa**, kéo hết chiều ngang ảnh. Cạnh trên đi
    qua đầu mút dưới của các vạch nghiêng dãy giữa (~(0, 577)→(960, 524)), cạnh dưới đi qua đầu mút trên của các vạch
    dãy tiền cảnh (~(0, 684)→(960, 596)). Polygon không ôm phần ô đỗ, không khoét quanh vạch sơn (sơn là mặt đường,
    không phải vật cản). Hai đầu trái/phải dừng ở mép ảnh vì lối chạy tiếp tục ra ngoài khung.
  - Polygon thứ hai là dải hẹp (cao ~6–10 px) giữa đầu các vạch dãy xa và đầu trên các vạch dãy giữa, quanh y≈491–522.
    Đây là lối nhìn thấy nhưng ở rất xa, trong vùng mặt đường cháy sáng; phía phải ảnh không còn đầu vạch để bám nên
    ranh của dải này kém chắc chắn hơn polygon chính.
  - Không có xe hay vật cản nào che lối chạy; xe đỏ ~(205, 467) nằm ngoài cả hai polygon.
- Ca chưa chắc cần hỏi người soát (nếu không có, ghi “không có”):
  - Các vạch ngắn ở **dãy ô phía xa** (khoảng y≈470–505, quanh chiếc xe đỏ ở ~(205, 467)): mặt đường vùng này bị
    cháy sáng, vạch nhỏ và độ tương phản thấp, nên khó xác định chính xác điểm đầu/cuối và khó chắc từng đoạn là
    vạch chia ô hay chỉ là vệt sơn. Cần người soát xác nhận vạch chia ô ở khoảng cách xa này có thuộc phạm vi gán
    nhãn không, và nếu có thì sai số vị trí chấp nhận được là bao nhiêu. Tôi vẫn giữ 6 polyline ở dãy này vì nhìn
    thấy được các đoạn sơn, nhưng độ chắc chắn thấp hơn các vạch ở dãy gần và dãy giữa.
  - Polygon `free_space` thứ hai ở dãy xa: nếu reviewer coi vùng quá xa/cháy sáng là ngoài phạm vi, polygon này nên bỏ;
    polygon lối chạy chính không phụ thuộc vào quyết định đó.
  - Ô đỗ trống cũng là mặt nhựa trống nhìn thấy được, nhưng tôi **không** đưa vào `free_space` vì định nghĩa của bài
    chỉ tính lối xe chạy (docs/11-parking-lines-vi.md). Nếu project coi ô trống là vùng đi được thì cần rule nói rõ.
