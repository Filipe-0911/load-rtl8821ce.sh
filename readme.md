# Problema ao iniciar driver de rede 

Recentemente atualizei meu linux mint para a versao 22 Wilma e, ao iniciar o sistema, percebi que não estava iniciando o driver de rede automaticamente.
Sendo assim, se você está com o mesmo problema, sugiro que torne o comando `sudo modprobe rtw88_8821ce` como um comando a ser executado ao iniciar o sistema.
Para isso, temos 3 opções:

### Método 1: Usar um Script de Inicialização

1. **Criar um Script no Diretório `/etc/init.d/`**

   - Crie um script com um nome descritivo, como `load-rtl8821ce.sh`:
     ```sh
     sudo nano /etc/init.d/load-rtl8821ce.sh
     ```

   - Adicione o seguinte conteúdo ao script:
     ```sh
     #!/bin/sh
     ### BEGIN INIT INFO
     # Provides:          load-rtl8821ce
     # Required-Start:    $network $local_fs
     # Required-Stop:     $network $local_fs
     # Default-Start:     2 3 4 5
     # Default-Stop:      0 1 6
     # Short-Description: Load rtl8821ce driver
     ### END INIT INFO

     case "$1" in
       start)
         echo "Loading rtl8821ce module..."
         modprobe rtw88_8821ce
         ;;
       stop)
         echo "Unloading rtl8821ce module..."
         modprobe -r rtw88_8821ce
         ;;
       *)
         echo "Usage: /etc/init.d/load-rtl8821ce.sh {start|stop}"
         exit 1
         ;;
     esac

     exit 0
     ```

   - Torne o script executável:
     ```sh
     sudo chmod +x /etc/init.d/load-rtl8821ce.sh
     ```

   - Adicione o script ao sistema de inicialização:
     ```sh
     sudo update-rc.d load-rtl8821ce.sh defaults
     ```

### Método 2: Adicionar ao Arquivo `/etc/modules`

Outra maneira de garantir que o módulo seja carregado automaticamente na inicialização é adicionar o nome do módulo ao arquivo `/etc/modules`.

1. **Editar o Arquivo `/etc/modules`**

   - Abra o arquivo `/etc/modules` com um editor de texto:
     ```sh
     sudo nano /etc/modules
     ```

   - Adicione o nome do módulo `rtw88_8821ce` no final do arquivo:
     ```plaintext
     rtw88_8821ce
     ```

   - Salve e saia do editor (Ctrl + O, Enter, Ctrl + X).

### Método 3: Usar um Serviço do Systemd

Se preferir usar um serviço do `systemd`, crie uma unidade de serviço para carregar o módulo.

1. **Criar um Arquivo de Unidade do Systemd**

   - Crie um arquivo de unidade para o módulo:
     ```sh
     sudo nano /etc/systemd/system/load-rtl8821ce.service
     ```

   - Adicione o seguinte conteúdo ao arquivo:
     ```plaintext
     [Unit]
     Description=Load rtw88_8821ce Module
     After=network.target

     [Service]
     Type=oneshot
     ExecStart=/sbin/modprobe rtw88_8821ce
     RemainAfterExit=yes

     [Install]
     WantedBy=multi-user.target
     ```

   - Salve e saia do editor (Ctrl + O, Enter, Ctrl + X).

   - Habilite e inicie o serviço:
     ```sh
     sudo systemctl enable load-rtl8821ce.service
     sudo systemctl start load-rtl8821ce.service
     ```

Cada um desses métodos garantirá que o módulo do driver seja carregado automaticamente a cada inicialização. Escolha o método que melhor se adapta às suas necessidades e ao seu sistema.