<div align="center">

# SpaceTech-IntoTheDeep

Documentação pública do processo de engenharia, projetos e impacto da equipe **SpaceTech 23504** durante a temporada **Into The Deep** do **First Tech Challenge (FTC)**.

[![FTC](https://img.shields.io/badge/FIRST%20Tech%20Challenge-Into%20The%20Deep-blueviolet)](https://www.firstinspires.org/robotics/ftc)
[![Status](https://img.shields.io/badge/Documentation-Public-success)]()
[![Language](https://img.shields.io/badge/Language-Portugu%C3%AAs-brightgreen)]()

</div>

---

## Sumário
- [Visão geral](#visão-geral)
- [Competições](#competições)
- [Mecânica](#mecânica)
- [Software](#software)
- - [Odometria](#odometria)
- - [Controle PID + FeedForward](#controle-pid--feedforward)
- - [Computação visual](#computação-visual)

---

## Visão geral

Durante a temporada **Into The Deep**, a equipe desenvolveu soluções integradas de mecânica, software, odometria e visão computacional para elevar o desempenho do robô em partidas autônomas e teleoperadas. Esta documentação apresenta os principais subsistemas, as tecnologias utilizadas e os resultados alcançados.

---

## Competições

### Run For The Robots Premier Event - Kentucky, EUA
- **Inspire Award**

### Brazil Championship - Brasília
- **Innovate Award sponsored by RTX** — 2º lugar

### Torneio Regional SESI de Robótica - Pernambuco
- **Winning Alliance - Captain**
- **Design Award**

---

## Mecânica

Em breve.

---

## Software

### Sensores utilizados
- 2x [Swingarm Odometry Pod](https://www.gobilda.com/swingarm-odometry-pod-48mm-wheel/).
- 1x [Pinpoint V2 Odometry Computer](https://www.gobilda.com/pinpoint-v2-odometry-computer-imu-sensor-fusion-for-2-wheel-odometry/).
- 2x [Touch Sensor](https://www.revrobotics.com/rev-31-1425/).
- 1x [LimeLight 3A CameraVision](https://stemos.com.br/produto/22036390/).

---

## Odometria

Utilizamos dois pods de odometria para estimar a posição do robô em tempo real em relação à arena, em conjunto com o Pinpoint Odometry Computer, que possui IMU integrada. Dessa forma, foi possível gerar a posição cartesiana do robô com coordenadas X e Y, além da sua orientação em graus.

<p align="center">
  <img width="500px" src="assets/images/bottom-view.png">
</p>

Com isso, foi possível criar trajetórias e aplicar algoritmos de controle durante o período autônomo, garantindo que o percurso realizado fosse sempre o mesmo, independentemente de variações do ambiente externo ou interno.

Com a biblioteca [Pedro Pathing](https://pedropathing.com/), os caminhos são gerados a partir de curvas de Bézier e recebem três controladores PID: lateral, frontal e de posição, além de uma correção de força centrípeta. Os caminhos também podem ser criados no [Pedro Pathing Visualizer](https://visualizer.pedropathing.com/).

[Assista ao teste da correção de caminhos](https://github.com/user-attachments/assets/5989fa0b-0b74-4dd0-9eb7-b81cfcb7481f)

Observe os primeiros testes da correção de caminhos em um primeiro protótipo:

[Assista ao protótipo](https://github.com/user-attachments/assets/792c633d-ddad-4b84-b477-c624ee670848)

<p align="center">
  <img width="1000px" src="assets/images/paths.jpg">
</p>

---

## Controle PID + FeedForward

Para o controle de posição dos mecanismos, como elevadores e extensores, utilizamos o controle Proporcional, Integral e Derivativo. Esse tipo de controle corrige o erro atual, considera o acúmulo de erro ao longo do tempo e reage à taxa de variação, reduzindo o tempo de resposta e melhorando a estabilidade do sistema.

$$u(t) = K_p e(t) + K_i \\int_{0}^{t} e(\\tau) d\\tau + K_d \\frac{de(t)}{dt}$$

Onde:
- \(e(t)\) é o erro, calculado como SP - PV, isto é, ponto de referência menos a entrada do sensor.
- \(K_p\) é o ganho proporcional, responsável por corrigir o erro atual.
- \(K_i\) é o ganho integral, responsável por corrigir o erro acumulado.
- \(K_d\) é o ganho derivativo, responsável por prever a tendência do erro com base em sua variação.

Os motores utilizados possuem encoders embutidos, que retornam a posição em ticks por revolução. Com um cálculo que leva em consideração a relação de redução utilizada, é possível converter esse valor em uma posição relativa da rotação do motor em ângulos.

```java
double CPR = [Ticks por revolução aqui];

// Pega a posição atual do motor
int position = motor.getCurrentPosition();
double revolutions = position / CPR;

double angle = revolutions * 360;
```

Entretanto, isso sozinho não foi suficiente, porque a temporada exigia um elevador vertical constantemente influenciado pela gravidade. Nesse cenário, o PID isolado se tornava difícil de ajustar. Por isso, foi implementado também o controle FeedForward, que funciona em malha aberta e fornece parte da energia necessária para o movimento antes mesmo que o erro apareça.

Ambos foram implementados utilizando a biblioteca [NextFTC-0.6](https://v0.nextftc.dev/user-guide/subsystems/lift).

### Resultado

[Assista ao resultado](https://github.com/user-attachments/assets/329a2a23-8ccb-4523-9826-523bd5d730a8)

---

## Computação visual

Utilizamos a Limelight 3A, uma câmera com integração de IA, para automatizar alguns processos durante o jogo. O objetivo era identificar as amostras de jogo e sua orientação, para definir a posição correta da garra e, assim, economizar tempo e aumentar a confiabilidade.

Utilizamos a biblioteca OpenCV e, com o ColorBlob Detector, identificamos regiões com grande quantidade de pixels de uma cor específica, como vermelho, azul ou amarelo, e criamos um contorno retangular nessa área. Se a largura do retângulo for maior que a altura, a amostra está na horizontal; caso contrário, está na vertical. A partir disso, criamos um gatilho para que a garra descesse na posição predefinida de coleta.

<p align="center">
  <img width="1000px" src="assets/images/limelight.jpg">
</p>

[Assista à detecção visual](https://github.com/user-attachments/assets/267b23d4-f4fb-4d22-b8fd-2ddf04a26233)

---

## Créditos
Projeto desenvolvido pela equipe **SpaceTech 23504**.