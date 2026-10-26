<div align="center">

# ZARTEC POCKET (ZK-01)
### Portable Multi-Band RF Analysis & Embedded Development Platform
**Zartec Labs // Hardware Division — Patos-PB, Brazil**

[![Hardware Revision](https://img.shields.io/badge/Hardware-Rev%201.2-gold?style=flat-square&logo=espressif)](https://zarteclabs.web.app)
[![Core](https://img.shields.io/badge/MCU-ESP32--S3%20N16R8%20(240MHz)-090d16?style=flat-square&logo=espressif)](https://zarteclabs.web.app)
[![Sub-GHz](https://img.shields.io/badge/RF-CC1101%20(300--928MHz)-10b981?style=flat-square)](https://zarteclabs.web.app)
[![2.4GHz](https://img.shields.io/badge/ISM-nRF24L01%2BPA%2BLNA%20(%2B20dBm)-f59e0b?style=flat-square)](https://zarteclabs.web.app)
[![Store Status](https://img.shields.io/badge/Batch%20%2301-Available%20for%20Dispatch-34d399?style=flat-square)](https://zarteclabs.web.app)

[🌐 Vitrine Oficial & Pedidos](https://zarteclabs.web.app) • [📋 Documentação Técnica LLM](https://zarteclabs.web.app/llms.txt) • [🛠 Acompanhar Pedido](https://zarteclabs.web.app)

</div>

---

## 1. Visão Geral da Plataforma

O **Zartec Pocket (ZK-01)** é um console portátil autônomo de análise de radiofrequência, auditoria de sinais e prototipagem embarcada projetado sobre o microcontrolador dual-core **Espressif ESP32-S3 N16R8**. 

Diferente de módulos experimentais em protoboard, o ZK-01 integra transceptor Sub-GHz, rádio 2.4 GHz amplificado, transceptor infravermelho, display IPS de alto contraste, leitor MicroSD e circuito de recarga LiPo em um chassi ergonômico fechado, calibrado e montado individualmente em bancada.


---

## 2. Especificações de Engenharia

### 2.1. Núcleo de Processamento & Memória
* **SoC:** Espressif ESP32-S3 (Xtensa Dual-Core 32-bit LX7 operando a até 240 MHz).
* **Aceleração Vetorial:** Instruções de ponto flutuante e DSP dedicadas.
* **Memória Não-Volátil (ROM):** 16 MB SPI Flash integrada (armazenamento de partições SPIFFS, NVS e firmware OTA).
* **Memória Volátil (RAM):** 512 KB SRAM interna + **8 MB Octal PSRAM** conectada via barramento de alta velocidade.

### 2.2. Subsistema de Rádio Sub-GHz
* **Transceptor:** Texas Instruments CC1101 (barramento SPI independente).
* **Faixas de Frequência Suportadas:** 300–348 MHz, 387–464 MHz (foco em 433.92 MHz ISM) e 779–928 MHz (868/915 MHz).
* **Modulações:** 2-FSK, GFSK, MSK, OOK e ASK.
* **Sensibilidade de Recepção:** -112 dBm a 1.2 kBaud.
* **Taxa de Dados Programável:** 0.6 a 600 kbps.

### 2.3. Subsistema de Rádio 2.4 GHz Amplificado
* **Chipset:** Nordic Semiconductor nRF24L01+ integrado a estágio amplificador de potência (PA) e pré-amplificador de baixo ruído (LNA).
* **Potência de Saída:** Até +20 dBm (100 mW EIRP).
* **Conector RF:** SMA Fêmea (50 Ohms) com antena omnidirecional rosqueável.
* **Filtragem de Alimentação:** Rede capacitiva de desacoplamento de baixa ESR (10µF Tântalo + 100nF Cerâmico MLCC) soldada junto aos pinos VCC/GND para eliminar flutuações de tensão durante picos de transmissão.

### 2.4. Subsistema Óptico (Infravermelho)
* **Recepção (RX):** Demodulador blindado VS1838B calibrado para portadora de 38 kHz.
* **Transmissão (TX):** LED infravermelho de 940 nm acionado por transistor chaveador para disparos direcionais de longo alcance (protocolos NEC, RC5, Sony, Samsung e RAW).

### 2.5. Interface de Usuário & Armazenamento
* **Visor:** TFT IPS de 2.0 polegadas, resolução nativa de 240 x 320 pixels, controlador ST7789VW acionado via SPI com suporte a DMA (Direct Memory Access).
* **Controles:** D-Pad direcional tátil (Up, Down, Left, Right, OK) + botões de ação física.
* **Armazenamento Secundário:** Leitor MicroSD via SPI com suporte a FAT32 para persistência de capturas `.sub`, dicionários e logs de espectro.

### 2.6. Gerenciamento de Energia
* **Célula:** Bateria LiPo 3.7V recarregável com circuito interno de proteção contra sobrecarga, sobredescarga e curto-circuito.
* **Interface de Carga:** USB Type-C com gerenciador linear TP4056 e corrente de carga regulada.
* **Chaveamento:** Chave mecânica de corte total (isolamento galvanico quando desligado, sem dreno residual).

---

## 3. Mapeamento de Barramentos & GPIOs

Para evitar contenção de barramento entre os múltiplos periféricos SPI de alta velocidade, as linhas de dados e seleção de chip foram distribuídas em canais dedicados:

| Periférico | Linha de Dados | Sinal de Clock | Chip Select (CS) | Controle Auxiliar |
| :--- | :--- | :--- | :--- | :--- |
| **Display ST7789** | MOSI (SPI Master) | SCLK (SPI Master) | `TFT_CS` | `DC`, `RST`, `BLK` (PWM) |
| **MicroSD SPI** | MOSI / MISO | SCLK | `SD_CS` | Detecção de cartão |
| **Sub-GHz CC1101** | MOSI / MISO | SCLK | `CC1101_CS` | `GDO0` (RX/TX IRQ), `GDO2` |
| **nRF24 + PA** | MOSI / MISO | SCLK | `nRF_CSN` | `nRF_CE` (Habilitação TX/RX) |
| **Infravermelho** | — | — | — | `IR_RX` (GPIO IRQ) / `IR_TX` |

---

## 4. Pilha de Software & Compatibilidade Open-Source

O hardware do Zartec Pocket foi construído deliberadamente sem travas proprietárias, garantindo compatibilidade total com os toolchains e bibliotecas consolidadas do ecossistema:

* **Frameworks:** Espressif ESP-IDF (nativo C/C++) e PlatformIO / Arduino Core para ESP32.
* **Radiofrequência Sub-GHz:** Biblioteca [`RadioLib`](https://github.com/jgromes/RadioLib) por Jan Gromes (gerenciamento completo do CC1101 em nível de registrador).
* **Protocolos 2.4 GHz:** Driver otimizado [`RF24`](https://github.com/nRF24/RF24) de alta performance.
* **Renderização Gráfica:** [`TFT_eSPI`](https://github.com/Bodmer/TFT_eSPI) com buffers de frame em DMA e aceleração na PSRAM.
* **Decodificação Infravermelha:** [`IRremoteESP8266`](https://github.com/crankyoldgit/IRremoteESP8266).

---

## 5. Aquisição & Disponibilidade de Bancada

Devido ao processo de soldagem manual de precisão, calibração RF e testes individuais de bancada em cada unidade, o Zartec Pocket é disponibilizado em lotes restritos:

* **Lote Atual:** `LOTE #01 (Zartec)` — 2 Unidades Disponíveis.
* **Portal Oficial de Aquisição:** [https://zarteclabs.web.app](https://zarteclabs.web.app)
* **Logística:** Frete Grátis com rastreamento Correios para todo o território nacional via SuperFrete.
* **Pagamento:** Liquidação instantânea via Pix oficial do Banco Central (Padrão EMVCo BR Code com CRC16).
* **Conteúdo do Pacote:**
  * 1x Console Zartec Pocket (ZK-01) montado e calibrado.
  * 1x Antena Sub-GHz dedicada (433.92 MHz).
  * 1x Antena 2.4 GHz de alto ganho com conector SMA.
  * 1x Cabo USB-C de dados de 4 vias devidamente testado em bancada.

---

## 6. Integridade & Diretrizes de Uso

> **Nota Ética e Legal:** O Zartec Pocket (ZK-01) é um instrumento de bancada educacional destinado a estudantes de engenharia, entusiastas de sistemas embarcados, desenvolvedores de IoT e pesquisadores de cibersegurança. Seu uso deve respeitar rigorosamente a legislação brasileira de telecomunicações (Resoluções da Anatel aplicáveis a equipamentos de radiação restrita nas faixas ISM).

---

<div align="center">
<sub>Desenvolvido com orgulho em Patos, Paraíba — Brasil • © Zartec Labs. Todos os direitos reservados.</sub>
</div>
