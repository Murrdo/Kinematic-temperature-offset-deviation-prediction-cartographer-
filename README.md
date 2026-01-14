# Kinematic-temperature-offset-deviation-prediction-cartographer-
BETA!

Набор макросов обеспечивающих адаптивный прогрев кинематики "больших" принтеров для обеспечения качественного первого слоя, не ожидая по 20-40 минут перед началом печати моделки 20*20мм.

Состоит из двух макросов:

1.FIRST_LAYER_ANALYSIS.cfg 
Принимает аргументы:
```
LINE_WIDTH=(ширина линии первого слоя)
SPEED=(скорость печати заполнения первого слоя)
ADD_PREP_TIME=(время на оставшуюся подготовку до начала печати)
```
Ширину и скорость можно либо указать свои стандартные "средние" либо брать из слайсера, например FLW={first_layer_infill_extrusion_width} FLS={first_layer_infill_speed} далее передавая их в этот макрос.

Так же рассчитывает примерную площадь первого слоя исходя из exclude_object (макрос должен вызываться ПОСЛЕ exclude_object).
Погрешность рассчёта составляет в районе 5-15% в большую сторону.
Итоговые значения сохраняются в "глобальных" переменных по имени макроса.


 2.offset_delta.cfg 

Принцип работы - раз в ХХ сек производит замер расстояния до стола,  высчитывает прогнозируемое изменение офсета за время подготовки к печати + времени первого слоя, если время меньше заданного порога - запускает следующий макрос в цепи стартоых. 
 
Аргументы:
```
CHECK_PERIOD = (период измерения офсета)
START_TH = (порог прогнозируемого офсета за время подготовки+печати первого при кором запускается следующий макрос)
BED_TARGET = (целевая темпиратура стола, можно не указывать, больше для текстов без печати)
FIRST_LAYER_TIME = (ожидаемое время слоя, если не указано в аргументах берется автоматически из переменных первого макроса)
REPEAT_TH = (доп. порог повторения проверки офсета)
NEXT_MACRO = (имя макроса запускаемого по завершению)
```

ОБЯЗАТЕЛЬНО должен запускаться в режиме простоя/пайузы, например можно разделить стартовый макрос на 2, и в конце первого перед вызовод данного макроса дать базовый PAUSE, т.к. макрос работает через delayed.

Пример работы 
```
TEMP_OFFSET_DELTA CHECK_PERIOD=60 START_OFFSET_THRESHOLD=0.03 GANTRY_TARGET=0 BED_TARGET=110 FIRST_LAYER_TIME=240 NEXT_MACRO = START_PRINT
> Temp offset monitoring start (carto), threshold = 0.03mm.
> First layer time 240.0s. => delta multiplier=4.
> OffsetΔ -0.033/60s TotalΔ -0.033/60s Bed 107.14° Cham 38.58° Gant 50.68°
> Estimated offset deviation on first layer = 0.132/0.03mm.
> OffsetΔ -0.016/60s TotalΔ -0.048/120s Bed 107.92° Cham 38.48° Gant 51.94°
> Estimated offset deviation on first layer = 0.064/0.03mm.
> OffsetΔ -0.011/60s TotalΔ -0.06/180s Bed 108.59° Cham 38.57° Gant 53.1°
> Estimated offset deviation on first layer = 0.044/0.03mm.
> OffsetΔ -0.011/60s TotalΔ -0.07/240s Bed 109.19° Cham 38.69° Gant 54.2°
> Estimated offset deviation on first layer = 0.044/0.03mm.
> OffsetΔ -0.008/60s TotalΔ -0.079/300s Bed 109.48° Cham 38.79° Gant 55.18°
> Estimated offset deviation on first layer = 0.032/0.03mm.
> OffsetΔ -0.007/60s TotalΔ -0.086/360s Bed 109.82° Cham 38.91° Gant 56.05°
> Estimated offset deviation on first layer = 0.028/0.03mm.
> START_PRINT
```




 
