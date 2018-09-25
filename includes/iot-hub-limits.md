Následující tabulka uvádí omezení spojená s různými úrovněmi služeb (S1, S2, S3, F1). Informace o nákladech na jednotlivé *jednotky* v každé úrovni najdete v tématu [Ceny služby IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Prostředek | S1 Standard | S2 Standard | S3 Standard | F1 Free |
| --- | --- | --- | --- | --- |
| Počet zpráv za den |400 000 |6 000 000 |300 000 000 |8 000 |
| Maximální počet jednotek |200 |200 |10 |1 |

> [!NOTE]
> Pokud očekáváte, že využijete více než 200 jednotek v centru úrovně S1, S2 nebo více než 10 jednotek v centru úrovně S3, kontaktujte podporu Microsoftu.
> 
> 

Následující tabulka uvádí omezení, která se vztahují na prostředky služby IoT Hub:

| Prostředek | Omezení |
| --- | --- |
| Maximální počet placených služeb IoT Hub na předplatné Azure |50 |
| Maximální počet bezplatných služeb IoT Hub na předplatné Azure |1 |
| Maximální počet znaků v Id zařízení | 128 |
| Maximální počet identit zařízení<br/> vrácených v jednom volání |1000 |
| Maximální doba uchování zpráv typu zařízení-cloud ve službě IoT Hub |7 dní |
| Maximální velikost zprávy typu zařízení-cloud |256 kB |
| Maximální velikost dávky typu zařízení-cloud |AMQP a HTTP: 256 KB pro celý batch <br/>MQTT: 256 KB pro každou zprávu |
| Maximální počet zpráv v dávce typu zařízení-cloud |500 |
| Maximální velikost zprávy typu cloud-zařízení |64 kB |
| Maximální hodnota TTL pro zprávy typu cloud-zařízení |2 dny |
| Maximální počet doručení zpráv typu <br/> cloud-zařízení |100 |
| Maximální počet doručení zpráv se zpětnou vazbou <br/> v reakci na zprávu typu cloud-zařízení |100 |
| Maximální hodnota TTL pro zprávy se zpětnou vazbou <br/> v reakci na zprávu typu cloud-zařízení |2 dny |
| Maximální velikost dvojčete zařízení <br/> (značky, ohlášené vlastnosti a požadované vlastnosti) | 8 kB |
| Maximální velikost řetězcové hodnoty dvojčete zařízení | 4 KB |
| Maximální hloubka objektu ve dvojčeti zařízení | 5 |
| Maximální velikost datové části přímé metody | 128 KB |
| Maximální doba uchování historie úlohy | 30 dní |
| Maximální počet souběžných úloh | 10 (pro S3), 5 (pro S2), 1 (pro S1) |
| Maximální počet dalších koncových bodů | 10 (pro S1, S2, S3) |
| Maximální počet pravidel směrování zpráv | 100 (pro S1, S2, S3) |


> [!NOTE]
> Pokud potřebujete více než 50 placených služeb IoT hub v rámci předplatného Azure, obraťte se na podporu Microsoftu.


> [!NOTE]
> Maximální počet zařízení, která se můžete připojit k jedno centrum IoT je v současné době 500 000. Pokud chcete tento limit zvýšit, obraťte se na [Microsoft Support](https://azure.microsoft.com/support/options/).

Služba IoT Hub omezuje žádosti při překročení následujících kvót:

| Omezení | Hodnota na centrum |
| --- | --- |
| Operace registru identit <br/> (vytvoření, načtení, výpis, aktualizace, odstranění), <br/> jednotlivý nebo hromadný import/export |(pro S3) 83.33/sec/Unit (5 000/min/jednotku) <br/> 1.67/sec/Unit (100/min/jednotku) (pro S1 a S2). |
| Připojení zařízení |6 000/s/jednotku (pro S3), 120/s/jednotku (pro S2), 12/s/jednotku (pro S1) <br/>Minimálně 100/s |
| Odesílání typu zařízení-cloud |6 000/s/jednotku (pro S3), 120/s/jednotku (pro S2), 12/s/jednotku (pro S1) <br/>Minimálně 100/s |
| Odesílání typu cloud-zařízení | 83.33/sec/Unit (5 000/min/jednotku) (pro S3), 1.67/sec/unit (100/min/jednotku) (pro S1 a S2). |
| Příjem typu cloud-zařízení |833.33/sec/Unit (50000/min/jednotku) (pro S3), 16.67/sec/unit (1 000/min/jednotku) (pro S1 a S2). |
| Operace nahrávání souborů |(pro S1 a S2) odeslat 83.33 soubor oznámení/s/jednotku (5 000/min/jednotku) (pro S3), souboru 1.67 odesílání oznámení/s/jednotku (100/min/jednotku). <br/> Najednou může existovat 10 000 identifikátorů URI SAS pro účet Azure Storage.<br/> Najednou může existovat 10 identifikátorů URI SAS/zařízení. |
| Přímé metody | 24MB/s/jednotku (pro S3), 480KB/s/jednotku (pro S2), 160KB/s/jednotku (pro S1)<br/> Podle velikosti 8KB omezení velikosti měřidla. |
| Čtení dvojčat zařízení | 50/s/jednotku (pro S3), maximálně 10/s nebo 1/s/jednotku (pro S2), 10/s (pro S1) |
| Aktualizace dvojčat zařízení | 50/s/jednotku (pro S3), maximálně 10/s nebo 1/s/jednotku (pro S2), 10/s (pro S1) |
| Operace úloh <br/> (vytvoření, aktualizace, výpis, odstranění) | (5 000/min/jednotku) (pro S3), 1.67/sec/unit 83.33/sec/Unit (100/min/jednotku) (pro S2), 1.67/sec/unit (100/min/jednotku) (pro S1) |
| Propustnost operací úloh jednotlivých zařízení | 50/s/jednotku (pro S3), maximálně 10/s nebo 1/s/jednotku (pro S2), 10/s (pro S1) |
