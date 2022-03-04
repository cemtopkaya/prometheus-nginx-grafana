# Ne var burada?

## Hangi uygulama hangi adreste?
Kimler hangi portlarda çalışıyor:
- Grafana : http://localhost:9999/ (Kullanıcı Adı: admin, Şifre: admin)
- Prometheus : http://localhost:9090/
- Node-exporter : http://localhost:9100/

## Hangi dosya ne ile ilgili?
- `grafana/datasources.yml` : Grafana içinde Prometheus sunucusunu bulması için.
- `grafana/dashboards/dashboard.yml` : Grafana'ya dashboard'ların dizinini içerir.
- `grafana/dashboards/node-exporter-full_rev26-1860.json` : `dashboards` Dizini içindeki göstergelerin (json dosyalarının) Grafana içinde görünmesi için [1860](https://grafana.com/grafana/dashboards/1860) ID'li **Node Exporter Full** isimli göstergenin [json](https://grafana.com/api/dashboards/1860/revisions/26/download) dosyasıdır.

## Nginx'in metrikleri nasıl çekilir? ([kaynak](https://www.tecmint.com/enable-nginx-status-page/))
Nginx, metrikleri dışarıya sunmak için   modülünü kullanır. Nginx çalıştığında ayarlar dosyasındaki varsayılan duruma göre bu modülü aktif veya pasif eder. 
Eğer modül faal değilse ise komutun çıktısı `with-http_stub_status_module` olacaktır:
```shell
$ nginx -V 2>&1 | grep -o with-http_stub_status_module
with-http_stub_status_module
```

Eğer modül faal ise:
```shell
$ nginx -V 2>&1 | grep -o with-http_stub_status_module

```


## Bağlantılar
- [prometheus.yaml Ayar dosyasında olabilecek job tanımlarına dair örnekler](https://github.com/prometheus/prometheus/blob/0a8d28ea932ed18e000c2f091200a46d2b62bac4/config/testdata/conf.good.yml)

Prometheus'u Ayaklandırmak

```shell
docker run --rm 
  -v ${PWD}/p4.yml:/etc/prometheus/prometheus.yml
  -p 9090:9090
  --name cpromc
  prom/prometheus 
    --config.file=/etc/prometheus/prometheus.yml 
    --storage.tsdb.path=/prometheus 
    --web.console.libraries=/usr/share/prometheus/console_libraries 
    --web.console.templates=/usr/share/prometheus/consoles 
    --log.level=debug

Tek satır >>> 
docker run -v ${PWD}/p4.yml:/etc/prometheus/prometheus.yml:rw -p 9090:9090 --name cpromc --rm prom/prometheus --config.file=/etc/prometheus/prometheus.yml --storage.tsdb.path=/prometheus --web.console.libraries=/usr/share/prometheus/console_libraries --web.console.templates=/usr/share/prometheus/consoles --log.level=debug
```

prometheus.yml
```yaml
global:
  scrape_interval: 5s

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:

  - job_name: "sabit-dosya"
    scheme: http
    metrics_path: '/metrics.html'
    static_configs:
    - targets: ["host.docker.internal:5500"]

  - job_name: "prometheus"
    # metrics_path defaults to '/metrics'
    scheme: https
    static_configs:
      - targets: ["prometheus.demo.do.prometheus.io"]

  - job_name: "repl"
    scheme: https
    static_configs:
    - targets: ["express-tutorial.cemt.repl.co"]
    tls_config:
      insecure_skip_verify: true
```

## nginx Exporter
Prometheus sunucusu ve metrik üreten bir nginx sunucusu ayakalnıyor.
Nginx sunucusu metrics isimli dosyayı 80 portundan sanki bir uygulamanın metriklerini sunuyor gibi yayımlıyor.
Nginx 80 dahili portunda çalışıyor ve hostun 80 portundan erişilebiliyor.
http://localhost:80/metrics


## PROMETHEUS
Prometheus varsayılan olarak `/metrics` adresine gider ve eğer farklı adrese gitmesi istenirse `metric_path` alanı job içinde tanımlanmalıdır:

```yaml
- job_name: "ornek job"
  metrics_path: /sunucu-adresine/ek-olacak/metrik-adresi
  static_configs:
    targets: ["sunucu-adresi.com", "192.168.1.19", ...]
```

Prometheus konteyneri içeride 8090 portunda çalışırken dışarıda 90 portundan erişilebilir. 
http://localhost:90

## Docker Komutları
Tüm konteynerleri >
Yansıları derlemek için:

```shell
docker-compose -f .\docker-compose.yaml build
```

Çalıştırmak için
```shell
docker-compose -f .\docker-compose.yaml up
```

Durdurmak için:

```shell
docker-compose -f .\docker-compose.yaml down
```
## GRAFANA
Sadece Grafayı ayaklandırmak için:
```shell
 docker run -d \
     -p 3000:3000 \
     --name=grafana \
     -e "GF_INSTALL_PLUGINS=grafana-clock-panel,grafana-simple-json-datasource" \
     grafana/grafana
```

Grafana'yı başlatmak için http://localhost:3000 adresine (kullanıcı adı ve şifresi "admin" ile) girilir ve Data Source kısmına Prometheus eklenerek Prometheus'un konteynerler arasında erişilebileceği URL adresi (http://cprom:9090) yazılarak "Save & Test" düğmesine basılarak eklenir.

