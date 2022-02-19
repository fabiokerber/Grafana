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
2. Influx **(v1.0)** roda em container no **grafana_srv**.<br>
3. Telegraf **(latest)** é executado no **centos_srv03** e envia as métricas ao Influx no **grafana_srv**.<br>
4. Para efeito de lab, **centos_srv03** possui apenas **um core** de CPU.<br>

# Grafana + InfluxDB + Telegraf
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/180220221600.png">
</kbd>
<br />
<br />

# Instalação inicial Grafana
```
> cd install
!! edit .env (GRAFANA_IP='192.168.0.245')
!! edit files/telegraf.conf (urls = ["http://192.168.0.245:8086"])
> vagrant up (irá subir a VM do grafana_srv e centos_srv03)

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

# InfluxDB e Telegraf
Dados já sendo coletados - telegraf (centos_srv03) > influxdb (grafana_srv).<br> 
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/180220221833.png">
</kbd>
<br />
<br />

# Configurar Data Source no Grafana
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/190220220900.png">
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

# Adicionando novo painel/query ao Dashboard
Variáveis são utilizadas para quando for criar novos dashboard, utilizar os mesmos valores padrão.<br>
**Refresh:** *On Time Range Change* - Quando alterar o valor de tempo na visualização, o Grafana atualiza o valor da variável em questão.<br>
**Query:** *SHOW TAG VALUES FROM system WITH KEY = host* - Exibe os valores da métrica **system** onde a chave for igual à **host**.<br>
Obs:<br>
1. Clicando fora dos campos, já é exibido um preview do valor coletado.<br>
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

# Criando novo painel de CPU (TOTAL/USO)
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
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/19022022091011.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/19022022091100.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/19022022091121.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/19022022091122.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/19022022091123.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/19022022091124.png">
</kbd>
<br />
<br />
$ stress-ng -c 0 -l 95

<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/19022022091126.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/19022022091127.png">
</kbd>
<br />
<br />