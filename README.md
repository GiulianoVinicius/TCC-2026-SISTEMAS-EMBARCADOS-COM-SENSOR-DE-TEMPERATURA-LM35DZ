# TCC 2026 — Sistema embarcado com sensor de temperatura LM35DZ

Projeto de sistema embarcado que mede a temperatura com um sensor **LM35DZ** e usa LEDs para indicar a faixa térmica. Quando a temperatura chega a **28 °C**, dois ventiladores são acionados por MOSFETs. O programa foi escrito para Arduino/C++.

## Como funciona

1. O microcontrolador lê o sinal analógico do LM35DZ no pino **A1**.
2. Para reduzir variações, `lerTemperatura()` faz 20 leituras, calcula a média e converte a leitura em tensão usando a referência interna configurada como **1,1 V**.
3. Como o LM35 fornece aproximadamente **10 mV por °C**, a temperatura é calculada por `tensão × 100`.
4. O LED indica a faixa de temperatura:
   - **Azul:** abaixo de 28 °C.
   - **Amarelo:** de 28 °C até abaixo de 30 °C.
   - **Vermelho:** a partir de 30 °C.
5. Os ventiladores ligam a partir de **28 °C** e desligam quando a temperatura fica abaixo de **27,5 °C**. Essa diferença de 0,5 °C (histerese) evita que eles fiquem ligando e desligando repetidamente perto do limite.
6. `setVentiladores()` aumenta ou reduz gradualmente o PWM dos ventiladores para suavizar a partida. A velocidade configurada é 255 (100%).
7. O monitor serial, a **9600 baud**, mostra a leitura ADC, tensão, temperatura, LED ativo e estado dos ventiladores. O programa também avisa quando a leitura está muito próxima dos extremos do ADC.

## Ligações usadas pelo código

| Componente | Pino do Arduino |
| --- | --- |
| Saída analógica do LM35DZ | A1 |
| LED azul | 7 |
| LED amarelo | 3 |
| LED vermelho | 5 |
| Controle do MOSFET do ventilador 1 | 9 (PWM) |
| Controle do MOSFET do ventilador 2 | 10 (PWM) |

Ligue cada LED com um resistor apropriado em série. Conecte os ventiladores por meio de MOSFETs adequados; **não os alimente diretamente pelos pinos do Arduino**. Use uma fonte compatível com os ventiladores e conecte o GND dessa fonte ao GND do Arduino. Confira a pinagem do LM35DZ e do MOSFET no datasheet dos componentes antes de energizar o circuito.

## Compilação e uso

1. Abra o arquivo `Código` na Arduino IDE (o arquivo contém o sketch C++ do projeto).
2. Selecione a placa e a porta serial corretas e carregue o programa.
3. Abra o Monitor Serial em **9600 baud** para acompanhar as medições e os avisos.

## Ajustes e observações

Os limites de temperatura e a velocidade dos ventiladores podem ser alterados nas constantes `TEMP_AMARELO`, `TEMP_VERMELHO`, `FOLGA_FANS` e `PWM_FANS`, no início do sketch.

A referência interna de 1,1 V depende da placa e pode variar entre unidades. Confirme se a placa escolhida oferece essa referência e calibre a medição se for necessária maior precisão. Com essa referência, a faixa de medição fica limitada aproximadamente a 0–110 °C; a tensão de saída do sensor não deve exceder a tensão de referência nem os limites da entrada analógica da placa.

## Estrutura do repositório

- `Código` — sketch Arduino que lê o sensor, controla LEDs e ventiladores e envia dados pela serial.
- `README.md` — descrição do projeto, funcionamento e orientações de ligação e uso.
