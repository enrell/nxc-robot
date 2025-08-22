## Projeto: OBR Resgate NXT — Template

Código em NXC (Not eXactly C) para um robô LEGO NXT orientado à competição de resgate.
O arquivo principal é `main.nxc` e implementa:
- Seguimento de linha com PID adaptativo
- Detecção e entrada em salas (fita prateada)
- Varredura em padrão S dentro da sala
- Aquisição e classificação de vítimas (brilho / cor)
- Planejamento simples para depósito (bins coloridos)

### Autor / Licença

Autor: Ênrell
Licença: MIT

### Hardware / Mapeamento de portas

Confira as definições no topo de `main.nxc`. Resumo:

- Sensores:
  - `PORT_LIGHT_RIGHT`  -> Sensor de luz direita (ex: IN_1)
  - `PORT_COLOR`        -> Sensor de cor / brilho (ex: IN_2)
  - `PORT_US`           -> Ultrassom (ex: IN_3)
  - `PORT_LIGHT_LEFT`   -> Sensor de luz esquerda (ex: IN_4)

- Motores:
  - `M_LEFT`   -> Motor esquerdo (OUT_A)
  - `M_RIGHT`  -> Motor direito (OUT_B)
  - `M_SHOVEL` -> Atuador da pá (OUT_C)
  - `M_DRIVE`  -> Macro para OUT_AB

Altere os defines no início do arquivo para adaptar sua montagem.

### Parâmetros importantes (onde ajustar)

- Limiares de luz: `TH_BLACK`, `TH_WHITE`, `TH_SILVER`, `TH_EXIT_BLACK`
- PID linha: `KP_LINE`, `KD_LINE`, `CURVE_TRIG`, `CURVE_HOLD`
- Velocidades: `V_LINE_BASE_FAST`, `V_LINE_BASE_SLOW`, `V_TURN`, etc.
- Ultrassom e distâncias: `US_OBS_AHEAD_CM`, `US_WALL_PERIM_CM`, `US_VICTIM_LOCK_CM`
- Tempo de varredura e número máximo de vítimas: `ROOM_TIMEOUT_MS`, `MAX_VICTIMS`

Faça pequenos testes com o robô para obter valores de calibração locais (iluminação e tipo de piso afetam bastante).

### Compilar / Enviar para o NXT


### Recomendação de ambiente de desenvolvimento

Use o VS Code com a extensão [nxc-syntax](https://github.com/enrell/nxc-syntax) para edição, sintaxe e realce de código NXC. É muito mais produtivo e moderno que a IDE BricxCC.

Para compilar e enviar o código ao NXT, utilize o CLI do compilador NBC:

```powershell
# compila main.nxc para arquivo executável .rxe
nbc -o main.rxe main.nxc
# envie o arquivo .rxe para o NXT usando sua ferramenta preferida (ex: nxt-tool, nbc uploader, etc)
```

**Resumo do fluxo recomendado:**
- Edite o código em VS Code com a extensão nxc-syntax
- Compile via CLI com NBC
- Envie o .rxe para o NXT

Evite o uso do BricxCC, pois é limitado e desatualizado.

### Execução e testes básicos

1. Ajuste os `#define` de portas conforme sua montagem.
2. Faça testes em bancada para calibrar `TH_BLACK` / `TH_WHITE` / `TH_SILVER` medindo valores dos sensores.
3. Teste o `lineFollowTurbo()` isoladamente com o robô ligado à trilha.
4. Teste aproximação e captura de vítima em área controlada antes de rodar em competição.

### Dicas de calibração rápidas

- Coloque o robô sobre as superfícies reais e imprima/registre `Sensor()` para cada sensor (ou observe leituras via BricxCC) enquanto varre preto/branco/prata.
- Aumente `BRIGHT_MIN_SILVER` se houver falsos positivos em áreas muito reflexivas.
- Reduza `V_APPROACH` e `V_TURN_FINE` durante testes de captura para evitar danos.

### Estrutura do código

O arquivo `main.nxc` já está organizado por seções:
- Configurações / defines
- Estruturas de estado (odometria, sensores, run state)
- Utilitários, motores, sensores, odometria
- Máquinas de estado: linha, perímetro, varredura, aquisição, depósito e saída
