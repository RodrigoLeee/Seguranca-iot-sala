# Atividade de Segurança em IoT

## Integrantes e contribuições
- Nataly Cunha: Criação da tabela e engajamento de ideias.
- Ian Simão - Criação da tabela e engajamento de ideias.
- Rodrigo Lee: Ajuste na tabela e engajamento de ideias.
- João Campos: Ajuste na tabela e engajamento de ideias.
- Lucas Brasil: Ajuste na tabela e engajamento de ideias e adição da tabela em Markdown.
- Isabelle Dantas: Ajuste na tabela e engajamento de ideias.
- Heitor Candido: Ajuste na tabela e engajamento de ideias.

### Vulnerabilidades

| **Vulnerabilidade**           | **Possíveis Ataques**                                                                                                          | **Impacto** | **Superfície de Ataque** |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------|-------------|---------------------------|
| **Utilização de um Broker Público** | Um agente externo pode interceptar o Broker e enviar mensagens maliciosas.                                                   | Alto        | MQTT                      |
| **CVE-2024-34351**             | Um atacante pode induzir o servidor a fazer requisições para destinos arbitrários, acessando recursos internos ou sensíveis.   | Alto        | Web                       |
| **Requisições HTTP**           | Sobrecarga do back-end e front-end com ataques DDoS de requisições massivas, tornando o site offline.                          | Alto        | Web                       |
| **WiFi**                       | Acesso irrestrito a dispositivos conectados à mesma rede, possibilitando ataques direcionados à rede e aos dispositivos.       | Alto        | WiFi                      |
| **Acesso à Memória do ESP32**  | Extração do código-fonte, chaves criptográficas ou dados sensíveis diretamente da memória do ESP32.                            | Alto        | Hardware                  |
| **CVE-2024-21538**             | manipulação de parâmetros dentro de funções de consulta.                                                                       | Alto        | Web                        |

### Detalhamento

#### **1. Utilização de um Broker Público - Heitor**
**Descrição:** Um agente externo pode interceptar o Broker MQTT e enviar mensagens maliciosas.

**Etapas:**
1. Analisou-se o Broker MQTT configurado para a aplicação.
2. Verificou-se que o Broker é público, permitindo acesso sem autenticação.
3. Foi demonstrado que, com ferramentas comuns, um atacante pode interceptar ou injetar mensagens maliciosas.
4. Essa vulnerabilidade permite controle indevido de dispositivos conectados e manipulação de dados críticos.

**Impacto:** Médio  
**Superfície de Ataque:** **MQTT**  
**Recomendação:**
- Configurar um Broker MQTT privado e seguro.
- Implementar autenticação com credenciais exclusivas para cada cliente.
- Habilitar comunicação criptografada (TLS).

---

#### **2. Servidor/Back-end (CVE-2024-34351 - Server-Side Request Forgery) - Ian**  
**Descrição:** Um atacante pode explorar essa vulnerabilidade para induzir o servidor a fazer requisições para destinos arbitrários, acessando recursos internos ou sensíveis.

**Etapas:**
1. Foi realizada análise das Server Actions utilizadas no back-end.
2. Detectou-se a presença de uma versão vulnerável do Next.js anterior à 14.1.1.

**Impacto:** Alto  
**Superfície de Ataque:** **Web**  
**Recomendação:**
- Atualizar o Next.js para a versão 14.1.1 ou superior.
- Implementar validação estrita no cabeçalho `Host` e nos redirecionamentos internos.

---

#### **3. Requisições HTTP - Heitor Candido**
**Descrição:** Vulnerabilidade a ataques de DDoS, onde requisições massivas podem sobrecarregar o back-end e o front-end, tornando o site indisponível.

**Impacto:** Alto  
**Superfície de Ataque:** **Web**  
**Recomendação:**
- Implementar rate limiting para limitar o número de requisições por IP em um período definido.
- Adicionar firewall inteligente para bloquear conexões de países onde a empresa não atua.
- Configurar proteção DDoS utilizando serviços como Cloudflare ou AWS Shield.

---

#### **4. WiFi - João Campos**
**Descrição:** Ao utilizar a rede WiFi da fazenda onde está instalado o dispositivo, é possível acessar todos os dispositivos conectados a essa rede, criando uma vulnerabilidade de rede.

**Impacto:** Alto  
**Superfície de Ataque:** **WiFi**  
**Recomendação:**
- Habilitar isolamento de dispositivos (Client Isolation) na configuração do roteador.
- Implementar segurança WPA3 e senhas fortes para evitar acessos não autorizados.

---

#### **5. Acesso à Memória e Extração do Código no ESP32 - Nataly**

**Descrição:**  
No ESP32, é possível acessar fisicamente a memória (principalmente a Flash externa, quando não criptografada), o que pode permitir a extração do firmware e de informações sensíveis, como chaves criptográficas. Além disso, se a interface de depuração (JTAG) estiver habilitada, um atacante com acesso ao hardware pode tentar ler a memória diretamente, obter o código-fonte e dados confidenciais.

**Impacto:** Alto  
**Superfície de Ataque:** Hardware

**Recomendações:**

1. **Criptografia de Flash**:  
   Utilize o recurso de **Flash Encryption** fornecido pela Espressif. Ao ativar essa funcionalidade, o conteúdo da memória Flash é armazenado criptografado, dificultando a extração do firmware e das chaves. Consulte a documentação oficial do ESP-IDF para configurar a criptografia da Flash:  
   [ESP-IDF Security Features - Flash Encryption](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/security/flash-encryption.html)

2. **Desativação de Depuração via eFuses**:  
   Queime os eFuses apropriados para desabilitar permanentemente o JTAG e outras interfaces de depuração. Uma vez programados, esses fusíveis não podem ser revertidos, impedindo o acesso não autorizado ao dispositivo:  
   [ESP32 eFuse Configuration](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/security/secure-boot.html#efuse-configuration)

4. **Proteção Física**:  
   Adote medidas para dificultar o acesso físico ao chip e à Flash externa. Por exemplo:
   - Selar a carcaça do dispositivo para dificultar a abertura.
   - Utilizar resinas, epóxi ou invólucros à prova de adulteração.

Com essas medidas, é possível mitigar significativamente o risco de extração não autorizada do código, chaves e dados sensíveis do ESP32.

---

#### **6. CVE-2024-21538: Vulnerabilidade no TypeORM - Rodrigo Lee**

**Descrição:**  
O TypeORM, até a versão 0.3.14, que está sendo utilizada no nosso projeto, apresentou uma vulnerabilidade que permitia a execução de código arbitrário ao processar entradas controladas por usuários maliciosos. A vulnerabilidade está relacionada à manipulação de parâmetros dentro de funções de consulta. Sem validações apropriadas, um atacante pode explorar a falha para comprometer o sistema.

**Impacto:** Alto  
**Superfície de Ataque:** Aplicações que utilizam o TypeORM para manipulação de bancos de dados.

**Recomendações:**  

1. **Atualização para a Versão Corrigida**:  
   Atualize o TypeORM para a versão 0.3.15 ou superior, onde a vulnerabilidade foi corrigida.  
   [GitHub TypeORM Releases](https://github.com/typeorm/typeorm/releases)

2. **Validação de Entradas**:  
   Implemente mecanismos robustos de validação e sanitização para todas as entradas de usuários antes de utilizá-las em consultas ao banco de dados.
