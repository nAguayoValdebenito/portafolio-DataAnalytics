





gh, oY, 2) LON apse iverani iB 



<!-- Start of picture text -->
Analisis Visual de Atipicos en Variables Climaticas<br>temperature_2m relative_humidity_2m precipitation wind_speed_10m<br>° 100 ° °<br>30 °<br>RD ° 60 8<br>25 8<br>20 80 10 i 50<br>8 40<br>15 wm<br>6 30<br>10<br>5 40 e 20<br>0 2 10<br>20<br>5 0) t)<br>temperature_2m relative_humidity_2m precipitation wind_speed_10m<br><!-- End of picture text -->

# DuocUC:: 



<!-- Start of picture text -->
Mapa de Calor: Mejores Variables Predictoras 1.00<br>temperature_2m 3m) 0.97 i 0.42 -0.24 | 0.44 -0.55<br>0.75<br>apparent_temperature 0.97 1.00 0.45 0.58 0.35, “0.24 -0.56 -0.40<br>j i 0.50<br>sunshine_duration 0.45 1.00 Fem -0.26 -0.07 (eens:<br>- 0.25<br>latitud ). AE 0.58 0.08 1.00 0.00 0.22 0.95 0.10<br>- 0.00<br>is day. O427 0.35 [ewe 0.00 meme -0.04 0.00 65)<br>- -0.25<br>cloud_cover- -0.24 0.24 0.26 0.22 -0.04 1.00 0.19 0.32<br>longitud ; -0.50<br>finial si)  -0.07 FROMM -0.00 0.19 BROOME -0.11<br>relative humidity 2m-+ 825 0.40= | suis: 0.10 -0.52 0.32 -0.11 1.00 -0.75<br>' ! ' '<br>= v = a=] > oD a=) E<br>v 5 5 & a a e £<br>Fe a a=] 3 2 a<br>5 E a! 2 E<br>2 8 § 3 2|<br>5i4Q<br>o& is}<br>fi] v<br><!-- End of picture text -->





<!-- Start of picture text -->
Pipeline<br>@®<br>» FeatureEngineeringClima<br>preprocesador: Columntransformer<br>2]<br>num cat remainder<br>+ SimpleImputer @ » SimpleImputer @ + passthrough<br>» StandardScaler @ » OneHotEncoder @<br>I<br>conversion: DataFrameConverter<br>preprocessor: ColumnTransformer<br>num cat remainder<br>+ SimpleImputer @ - SimpleImputer @ + passthrough<br>» StandardScaler @ » OneHotEncoder @<br>|<br>|<br><!-- End of picture text -->

Ulete 



<!-- Start of picture text -->
QF en<br>Ulete ee<br><!-- End of picture text -->



<!-- Start of picture text -->
Visualizacion del Ajuste: Valores Reales vs. Predicciones (Regresion Lineal)<br>30 —= Ajuste Perfecto (Ideal) 77@6<br>e a“<br>e<br>-<br>3 35 ae. oli<br>‘°° * genlees,<br>% 20 sete.<br>= opeeet<br>= oe,alee<br>a 8 a a ye<br>‘a Speers §<br>e oy yf ®<br>© mo<br>£ 20 aeons, ©<br>o 2 2a is<br>5 e Po ead *e<br>2 tay = SO<br>8 5 - Megas”<br>@ -sigee*<br>2 0 e a Ct a . i<br>e “3 @<br>-5 he<br>—5 ft) 5 10 15 20 25 30<br>Temperatura Real (°C)<br><!-- End of picture text -->



### VERIFICACION DE RESTRICCIONES PARA LOS 3 MODELOS 

|Modelo:<br>Lo|gisticRe|gressio|n|||
|---|---|---|---|---|---|
|ROC-AUC|Entrenam|iento<br>:|8.2592|||
|ROC-AUC|Prueba|:|8.8531|->|CUMPLE (>=6.79)|
|Diferenc|ia train|-test<br>:|@.0061|->|CUMPLE: Sin overfitting|
|Fl-Score|(test,|t=0.5):|0.5363|->|CUMPLE (>=6.48)|
|Modelo:<br>De|cisionTr|eeClassi|ftier|||
|ROC-AUC|Entrenam|iento<br>:|6.9037|||
|ROC-AUC|Prueba|:|@.8788|->|CUMPLE (>=8.79)|
|Diferenc|ia train|-test<br>:|6.0249|->|CUMPLE: Sin overfitting|
|Fi-Score|(test,|t=@.5):|@.5876|->|CUMPLE (>=0.48)|
|Modelo:<br>Ga|ussianNB|||||
|ROC-AUC|Entrenam|iento<br>:|6.3249|||
|ROC-AUC|Prueba|:|@.8201|->|CUMPLE (>=8.79)|
|Diferenc|ia train|-test<br>:|@.6049|->|CUMPLE: Sin overfitting|
|Fl-Score|(test,|t=@.5):|@.508@|->|CUMPLE(>=@.48)|



## Matriz de Confusion (DecisionTreeClassifier | Threshold=0.5) 



<!-- Start of picture text -->
2 4239 1145<br>§<br>a - 236 984<br>!!<br>01<br>Prediccién<br><!-- End of picture text -->





<!-- Start of picture text -->
Método del Codo para K-Means (Segmentacién Climatica)<br>200000 @ —  Codo Kneed (K=5)<br>4<br>\<br>8 180000 .<br>oO ‘<br>3‘<br>bedS160000 :\<br>ai<br>w \<br>3 140000 .<br>Ww hs<br>= Me<br>g TK<br>5 120000 a.<br>aau| ~ ae<br>= 100000 a<br>a Lon<br>4 *s<br>& 80000 aad ‘oo<br>5 ta<br>£ aie<br>60000 aeees<br>2 4 6 8 10<br>Numero de Clusters (kK)<br><!-- End of picture text -->



<!-- Start of picture text -->
Visualizacion de Perfiles Climaticos (PCA 2D)<br>144 Cluster Climatico<br>® oO<br>el<br>12 ® 2<br>s 3<br>fis]<br>=4<br>210<br>WwMPadN2 ® ’# :<br>£5 ‘<br>gs :<br>S ° e ws ar .)<br>i oases Rete, "<br>3 ee eo 6 oy ae ,<br>: * ae ee faneS<br>vy Sooo 0d POOLR RASS Sesion my Cue teece. fotcobie, SS<br>E eo. @se eeRO Ticis |LG eS ae<br>© @ Cte ae res gas ugesewereee a ekene‘6SEINais o<br>—4 -2 fs) 2 4<br>Componente Principal 1 (40.21% Varianza Explicada)<br><!-- End of picture text -->



SSEMER 

| 

A 



<!-- Start of picture text -->
NH “s<br>QF en<br><!-- End of picture text -->

