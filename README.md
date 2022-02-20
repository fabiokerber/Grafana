# Grafana

Pré requisito:

|Tool    |Link|
|-------------|-----------|
|`Vagrant`| https://releases.hashicorp.com/vagrant/2.2.19/vagrant_2.2.19_x86_64.msi
|`VirtualBox`| https://download.virtualbox.org/virtualbox/6.1.30/VirtualBox-6.1.30-148432-Win.exe

# Geral
Dashboard é uma coleção de painéis.<br>
Painel é um mostrador de métricas.<br>
O banco a ser utilizado é o **InfluxDB**. Solução de **Time Series Database (TSDB)**, mais leve para gravações simples de métricas.<br>
O coletor de métricas a ser utilizado será o **Telegraf**. O mesmo tem total compatibilidade com o InfluxDB e claro, deve ser instalado no host à ser monitorado.<br>
Obs:<br>
1. Grafana **(latest)** roda em container no **grafana_srv**.<br>
2. Influx **(v2.1.1)** roda em container no **grafana_srv**.<br>
3. Telegraf **(latest)** é executado no **centos_srv03** e envia as métricas ao Influx no **grafana_srv**.<br>
4. Para efeito de lab, **centos_srv03** possui apenas **um core** de CPU.<br>
5. Container NGINX:8080 **(latest)** é executado no **centos_srv03** apenas para efeitos de monitoramento.<br>
6. Apache:80 **(latest)** é executado no **centos_srv03** apenas para efeitos de monitoramento.<br>

# Grafana + InfluxDB + Telegraf
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/180220221600.png">
</kbd>
<br />
<br />

# Instalação inicial InfluxDB
http://192.168.0.245:8086/<br>
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220222122.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220222123.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220222124.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220222125.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220222126.png">
</kbd>
<br />
<br />
unix:///var/run/docker.sock<br>
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220222127.png">
</kbd>
<br />
<br />
Anotar info!<br>
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220222129.png">
</kbd>
<br />
<br />

# Alterar configurações iniciais e start grafana_srv e centos_srv03
```
> cd install
!! edit .env 
    GRAFANA_IP='192.168.0.245'

!! edit files/telegraf.conf
    [[outputs.influxdb_v2]]
      urls = ["http://192.168.0.245:8086"]

    [[inputs.docker]]
      endpoint = "tcp://[ip]:[port]" - Monitorar Docker em outro host a partir do telegraf local, necessário expor a porta do serviço do docker.

    Conteúdo arquivo override.conf para expor porta Docker:
    [Service]
    ExecStart=
    ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2376
    
    $ sudo mkdir -p /etc/systemd/system/docker.service.d/
    $ sudo cp /vagrant/configs/override.conf /etc/systemd/system/docker.service.d/
    $ sudo systemctl daemon-reload
    $ sudo systemctl restart docker.service

    https://github.com/influxdata/telegraf/blob/master/docs/DATA_FORMATS_INPUT.md
    https://github.com/influxdata/telegraf/tree/master/plugins/parsers/grok
    https://github.com/influxdata/telegraf/blob/master/plugins/inputs/logparser/README.md

> vagrant up (irá subir a VM do grafana_srv e centos_srv03)
```

# Inserir token no serviço telegraf executando em centos_srv03
```
> cd install
> vagrant ssh centos_srv03
    $ sudo vim /etc/telegraf/telegraf.conf
        [[outputs.influxdb_v2]]
          ...
          token = "$INFLUX_TOKEN"

    $ sudo systemctl restart telegraf
```

# Verificar no InfluxDB se estão chegando os dados de monitoramento
```
http://192.168.0.245:8086
```
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220222248.png">
</kbd>
<br />
<br />

# Configurar Data Source no Grafana
Antes de configurar o Data Source, é necessário anotar o admin's Token!<br>
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220222259.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220222300.png">
</kbd>
<br />
<br />
Agora acesse o Grafana e configure o Data Source.<br>
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220220900.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220222258.png">
</kbd>
<br />
<br />

# Acessar o Grafana
```
http://192.168.0.245:3000
    admin | (admin)
```

# Primeira Dashboard
Adicionar painel e Query.<br>
Query **Random Walk**, são números gerados de forma aleatória apenas para exemplo de gráfico mesmo.<br> 
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/180220221516.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/180220221518.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/180220221523.png">
</kbd>
<br />
<br />

# Importar Dash pronta do InfluxDB
New Dashboard > Import <br>
https://grafana.com/grafana/dashboards/11912<br>
Obs:<br>
1. Necessário ajustar telegraf.conf
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221317.png">
</kbd>
<br />
<br />

# Novo Dashboard utilizando variáveis
Variáveis são utilizadas para quando for criar novos dashboard, utilizar os mesmos valores padrão.<br>
**Refresh:** *On Time Range Change* - Quando alterar o valor de tempo na visualização, o Grafana atualiza o valor da variável em questão.<br>
**Query:** *SHOW TAG VALUES FROM system WITH KEY = host* - Exibe os valores da métrica **system** onde a chave for igual à **host**.<br>
Obs:<br>
1. Clicando fora dos campos, já é exibido um preview do valor coletado.<br>
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220220911.png">
</kbd>
<br />
<br />

200220220753

# Painel de Uptime
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220220924.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220220925.png">
</kbd>
<br />
<br />

1. FROM: **system** WHERE: **host** = **$servers**<br>
2. SELECT: **field(uptime)**<br>
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220220926.png">
</kbd>
<br />
<br />

Alterar para **Stat**.<br>
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220220940.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220220949.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220220950.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220220951.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220220952.png">
</kbd>
<br />
<br />

# Painel de CPU (% EM USO)
*usage_idle* - Memória disponível.<br>
*math(*-1 + 100)* - Fórmula matemática para trazer a porcentagem total de Memória disponível.<br> 
1. FROM: **cpu** WHERE: **host** = **$servers** AND: **cpu** = **cpu-total**<br>
2. SELECT: **field(usage_idle)** **math(*-1 + 100)**<br>
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221007.png">
</kbd>
<br />
<br />

Alterar para **Gauge**.<br>
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221011.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221100.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221121.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221122.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221123.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221124.png">
</kbd>
<br />
<br />
```
$ stress-ng -c 0 -l 95
```

<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221126.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221127.png">
</kbd>
<br />
<br />

# Painel de DISCO (% EM USO)
1. FROM: **disk** WHERE: **host** = **$servers** AND: **path** = **/**<br>
2. SELECT: **field(used_percent)**<br>
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221130.png">
</kbd>
<br />
<br />

Selecionar **Bar gauge**.<br>
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221131.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221135.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221136.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221137.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221138.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221140.png">
</kbd>
<br />
<br />

# Painel de MEMÓRIA (TOTAL EM USO)
1. FROM: **mem** WHERE: **host** = **$servers**<br>
2. SELECT: **field(used_percent)**<br>
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221151.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221205.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221206.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221207.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221208.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221209.png">
</kbd>
<br />
<br />

```
$ stress-ng --vm-bytes $(awk '/MemAvailable/{printf "%d\n", $2 * 0.9;}' < /proc/meminfo)k --vm-keep -m 1
$ stress-ng --vm 2 --vm-bytes 256M --timeout 240s
```

# Painel de monitoramento de containers
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221557.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221600.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221607.png">
</kbd>
<br />
<br />

# Painel de monitoramento de logs de aplicação (Apache)
Serão monitorados os logs do apache contidos em /var/log/httpd/ <br>
Neste caso não será habilitado um input próprio para o apache, mas sim um módulo do telegraf que analisa/"parseia" logs.<br>
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220221557.png">
</kbd>
<br />
<br />