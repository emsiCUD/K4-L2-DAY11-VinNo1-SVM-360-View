# So sánh L với R

Chỉ số L/R là thứ tự box cao ≥ H=40 trong từng frame, theo thứ tự XML; bắt đầu từ 1.
Box L trong ignore_region được báo IGNORE_SCOPE, không tính SPURIOUS.

## adasind_145860.jpg
- L2 mid IGNORE_SCOPE
## adasind_167700.jpg
- L4 mid IGNORE_SCOPE
- L9 mid IGNORE_SCOPE
- L1+R9 mid BOX_GEOMETRY
- L5+R4 center WRONG_CLASS
- R2 mid MISSING
- R8 center MISSING
## adasind_199770.jpg
- L4 mid IGNORE_SCOPE
- L1 center SPURIOUS
- L3 mid SPURIOUS
- L5+R9 center BOX_GEOMETRY
- L9+R5 edge BOX_GEOMETRY
- R3 mid MISSING
- R4 mid MISSING
- R6 edge MISSING

## Theo zone
| zone | n_ref | matched | missing | spurious |
|---|---|---|---|---|
| center | 9 | 6 | 3 | 3 |
| mid | 7 | 3 | 4 | 2 |
| edge | 4 | 2 | 2 | 1 |
