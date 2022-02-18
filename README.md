# Grafana

Pré requisito:

|Tool    |Link|
|-------------|-----------|
|`Vagrant`| https://releases.hashicorp.com/vagrant/2.2.19/vagrant_2.2.19_x86_64.msi
|`VirtualBox`| https://download.virtualbox.org/virtualbox/6.1.30/VirtualBox-6.1.30-148432-Win.exe

# Geral
Dashboard é uma coleção de painéis.<br>
Painel é um mostrador de métricas.<br>
O banco a ser utilizado é o Influx. Solução de Time Series Database (TSDB), mais leve para gravações simples de métricas.<br>
O coletor de métricas a ser utilizado será o Telegraf. O mesmo tem total compatibilidade com o InfluxDB e claro, deve ser instalado no host à ser monitorado.<br>
Obs:<br>
1. Influx (v1.0) já é configurado em container via vagrant no grafana_srv.<br>
2. Telegraf já instalado via vagrant no grafana_srv.<br>

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
Query "Random Walk", são números gerados de forma aleatória apenas para exemplo de gráfico mesmo.<br> 
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