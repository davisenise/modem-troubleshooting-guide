# Guia de Diagnóstico — Problemas Comuns em Modem/Roteador

**Autor:** Davi Senise
**Cargo:** Técnico de Suporte TI (N1/N2)
**Repositório:** modem-troubleshooting-guide

---

## Objetivo

Guia de referência rápida para diagnóstico e resolução dos problemas mais comuns em modems e roteadores, cobrindo desde acesso ao painel até queda total de conexão. Feito pra consulta em campo (helpdesk, suporte remoto) sem precisar reconstruir o raciocínio toda vez.

Cada seção segue o mesmo padrão: **Sintoma → Causa provável → Como confirmar → Solução → Como evitar de novo.**

---

## Índice

1. [Não consigo acessar o painel administrativo](#1-não-consigo-acessar-o-painel-administrativo)
2. [IP do gateway não responde (ping falha)](#2-ip-do-gateway-não-responde-ping-falha)
3. [Sem internet, mas Wi-Fi conecta normalmente](#3-sem-internet-mas-wi-fi-conecta-normalmente)
4. [Internet lenta / instável](#4-internet-lenta--instável)
5. [Modem trava e precisa reiniciar com frequência](#5-modem-trava-e-precisa-reiniciar-com-frequência)
6. [Sem acesso físico ao equipamento](#6-sem-acesso-físico-ao-equipamento)
7. [Wi-Fi visível mas sem sinal / conexão cai sozinha](#7-wi-fi-visível-mas-sem-sinal--conexão-cai-sozinha)
8. [Checklist rápido de coleta de dados](#8-checklist-rápido-de-coleta-de-dados)

---

## 1. Não consigo acessar o painel administrativo

**Sintoma:** Digita o IP do modem no navegador e a página não abre, ou pede login que ninguém tem.

**Causas prováveis:**
- Modem fornecido pela operadora com acesso de usuário final bloqueado por padrão
- Credenciais nunca foram alteradas da etiqueta (que ninguém tem em mãos)
- IP digitado é de outro equipamento da rede (modem ≠ roteador secundário)

**Como confirmar:**
```
ipconfig
```
Verifica o campo **Gateway Padrão** — esse é o IP correto do primeiro salto da rede, não o que você acha que é.

**Solução:**
- Testa `http://` e `https://` no IP correto
- Testa credenciais padrão do fabricante (variam por marca — nunca assuma)
- Se for equipamento de operadora (Vivo, Claro, etc.) e não abrir mesmo com IP certo: não adianta insistir. Acesso de usuário final costuma vir travado de propósito. Aciona suporte técnico da operadora.

**Como evitar de novo:**
Documentar IP, usuário e senha de todo equipamento de rede assim que é instalado — antes que a informação se perca.

---

## 2. IP do gateway não responde (ping falha)

**Sintoma:** `ping` no IP do gateway retorna "tempo limite esgotado" ou "host de destino inacessível".

**Causas prováveis:**
- Equipamento desligado ou travado
- PC está em segmento de rede diferente do equipamento que você está tentando acessar
- Cabo rompido ou porta com defeito

**Como confirmar:**
```
ipconfig
arp -a
```
O `arp -a` mostra os dispositivos que respondem na rede local — se o IP que você quer nem aparece, ele não está acessível daqui.

**Solução:**
- Confirma que o gateway padrão do `ipconfig` bate com o IP que você está tentando pingar (se for diferente, você está testando o equipamento errado)
- Se bater e mesmo assim não responder: sem acesso físico, a única saída é reinício remoto via operadora (TR-069) ou intervenção presencial

**Como evitar de novo:**
Mapear a topologia da rede (quem é modem, quem é roteador secundário, quais IPs cada um usa) evita perder tempo testando IP errado.

---

## 3. Sem internet, mas Wi-Fi conecta normalmente

**Sintoma:** Dispositivo conecta na rede Wi-Fi, mas nenhuma página carrega.

**Causas prováveis:**
- Falha entre modem e provedor (fibra/cabo rompido, instabilidade da operadora)
- DNS não está resolvendo
- IP obtido é de rede local isolada (sem rota pra internet)

**Como confirmar:**
```
ipconfig
ping 8.8.8.8
ping google.com
```
- Se `ping 8.8.8.8` funciona mas `ping google.com` falha → problema é DNS
- Se os dois falham → problema é de conectividade real (modem ou operadora)

**Solução:**
- DNS: troca manualmente pro DNS público (`8.8.8.8` / `1.1.1.1`) como teste
- Conectividade: verifica luzes do modem (se tiver acesso físico) — luz de internet apagada ou piscando geralmente indica problema do lado da operadora
- Sem solução local → aciona operadora

**Como evitar de novo:**
Manter um DNS secundário configurado sempre reduz esse tipo de chamado pela metade.

---

## 4. Internet lenta / instável

**Sintoma:** Conexão funciona mas com lentidão, perda de pacotes ou latência alta.

**Causas prováveis:**
- Excesso de dispositivos conectados simultaneamente
- Interferência de Wi-Fi (canal saturado, muitos vizinhos na mesma frequência)
- Modem superaquecido ou desatualizado
- Problema de banda contratada vs. demanda real

**Como confirmar:**
```
ping -t 8.8.8.8
tracert google.com
```
Latência alta e/ou perda de pacotes no `ping` contínuo indica instabilidade real, não impressão.

**Solução:**
- Testa em cabo (elimina Wi-Fi da equação)
- Se lento só no Wi-Fi → troca de canal (2.4GHz costuma ter mais interferência que 5GHz)
- Se lento até em cabo → contata operadora com print do teste de velocidade em mãos

**Como evitar de novo:**
Reposicionar o roteador longe de paredes/eletrônicos e revisar a banda contratada conforme a quantidade de usuários simultâneos.

---

## 5. Modem trava e precisa reiniciar com frequência

**Sintoma:** A cada X dias/horas o modem para de responder e só volta com reinício manual.

**Causas prováveis:**
- Firmware desatualizado
- Superaquecimento (local mal ventilado)
- Excesso de dispositivos/conexões simultâneas sobrecarregando o hardware
- Equipamento de vida útil já esgotada

**Como confirmar:**
Verificar se existe padrão (mesmo horário, após certa carga de uso, calor ambiente).

**Solução:**
- Verifica se há atualização de firmware disponível no painel
- Melhora ventilação/posicionamento físico
- Se for equipamento de operadora com histórico de falha recorrente: solicita troca de aparelho, não só reinício

**Como evitar de novo:**
Programar reinício automático periódico (via smart plug ou configuração do próprio modem, quando suportado) evita que o problema vire chamado recorrente.

---

## 6. Sem acesso físico ao equipamento

**Sintoma:** Precisa reiniciar/configurar o modem, mas ele está trancado, em local inacessível, ou sem alguém no local.

**Causas prováveis:**
Situação estrutural, não técnica — mas exige rota alternativa de resolução.

**Solução (em ordem de prioridade):**
1. **Painel web:** se a rede permite acesso, reinicia via painel administrativo remotamente
2. **TR-069 / gestão remota da operadora:** se o modem é fornecido pela operadora, o suporte técnico consegue reiniciar do lado deles — é literalmente pra isso que esse protocolo existe
3. **Tomada inteligente:** solução preventiva — se configurada previamente, permite cortar energia remotamente via app

**Como evitar de novo:**
Instalar uma tomada smart no ponto do modem antes do problema acontecer é a única forma de ter controle remoto real de hardware sem depender de terceiros.

---

## 7. Wi-Fi visível mas sem sinal / conexão cai sozinha

**Sintoma:** Rede aparece na lista, conecta, mas cai sozinha depois de alguns minutos.

**Causas prováveis:**
- Limite de dispositivos conectados simultaneamente atingido
- Interferência de canal
- Driver de rede desatualizado no dispositivo cliente (não no modem)
- Antena ou hardware do roteador com defeito

**Como confirmar:**
Testar se o problema acontece em mais de um dispositivo (isola se é o roteador ou só um cliente específico).

**Solução:**
- Se só um dispositivo cai → atualiza driver de rede dele
- Se todos os dispositivos caem → reinicia roteador, troca canal, revisa quantidade de conexões simultâneas
- Persistindo → equipamento com defeito de hardware, solicitar troca

**Como evitar de novo:**
Monitorar quantidade de dispositivos conectados e revisar se o roteador comporta a demanda da rede.

---

## 8. Checklist rápido de coleta de dados

Antes de abrir qualquer chamado ou escalar pra operadora, ter em mãos:

- [ ] Gateway padrão (`ipconfig`)
- [ ] Resultado de `ping` no gateway
- [ ] Resultado de `ping 8.8.8.8` e `ping google.com`
- [ ] Modelo/fabricante do equipamento (se visível)
- [ ] Se é modem de operadora ou equipamento próprio
- [ ] Se há acesso físico ao aparelho
- [ ] Horário de início do problema e se há padrão de recorrência

Ter essas respostas prontas reduz o tempo de atendimento — tanto o seu quanto o da operadora.

---

## Notas

Este guia é vivo: conforme novos padrões de falha aparecem em atendimentos reais, novas seções devem ser adicionadas seguindo a mesma estrutura (Sintoma → Causa → Confirmação → Solução → Prevenção).
