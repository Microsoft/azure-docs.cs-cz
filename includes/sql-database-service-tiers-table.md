<!--
Used in:
sql-database-performance-guidance.md 
sql-database-single-database-resources.md 
-->

### <a name="basic-service-tier"></a>Úroveň služeb Basic
| **Úroveň výkonu** | **Basic** |
| :--- | --: |
| Maximální počet DTU | 5 |
| Zahrnuté úložiště (GB) | 2 |
| Maximální počet možností úložiště (GB) | 2 |
| Maximální úložiště OLTP v paměti (GB) |neuvedeno |
| Maximální počet souběžných pracovních procesů (počet požadavků) | 30 |
| Maximální počet souběžných přihlášení | 30 |
| Maximální počet souběžných relací | 300 |
|||

### <a name="standard-service-tier"></a>Úroveň služeb Standard
| **Úroveň výkonu** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|---:|
| Maximální počet jednotek Dtu ** | 10 | 20 | 50 | 100 |
| Zahrnuté úložiště (GB) | 250 | 250 | 250 | 250 |
| Maximální počet možností úložiště (GB) * | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maximální úložiště OLTP v paměti (GB) | neuvedeno | neuvedeno | neuvedeno | neuvedeno |
| Maximální počet souběžných pracovních procesů (počet požadavků)| 60 | 90 | 120 | 200 |
| Maximální počet souběžných přihlášení | 60 | 90 | 120 | 200 |
| Maximální počet souběžných relací |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Úroveň služby na úrovni Standard (pokračování)
| **Úroveň výkonu** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|---:|
| Maximální počet jednotek Dtu ** | 200 | 400 | 800 | 1600 | 3000 |
| Zahrnuté úložiště (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximální počet možností úložiště (GB) * | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maximální úložiště OLTP v paměti (GB) | neuvedeno | neuvedeno | neuvedeno | neuvedeno |neuvedeno |
| Maximální počet souběžných pracovních procesů (počet požadavků)| 400 | 800 | 1600 | 3200 |6000 |
| Maximální počet souběžných přihlášení | 400 | 800 | 1600 | 3200 |6000 |
| Maximální počet souběžných relací |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Úroveň služeb Premium 
| **Úroveň výkonu** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| Maximální počet DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Zahrnuté úložiště (GB) | 500 | 500 | 500 | 500 | 4 096 | 4 096 |
| Maximální počet možností úložiště (GB) * | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4 096 | 4 096 |
| Maximální úložiště OLTP v paměti (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maximální počet souběžných pracovních procesů (počet požadavků)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maximální počet souběžných přihlášení | 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maximální počet souběžných relací | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

> [!IMPORTANT]
> \* Velikosti úložiště větší než velikost zahrnutého úložiště jsou ve verzi Preview a účtují se za ně další poplatky. Podrobnosti najdete na stránce s [cenami služby SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
>
>\* Ve vrstvě | Premium je více než 1 TB úložiště aktuálně k dispozici v následujících oblastech: Brazílie – Jih, Střední Kanada, Východní Kanada, střed USA, Francie centrální, Německo centrální, Japonsko – východ, Japonsko – Západ, Korejská centrální, – Sever střední USA, Severní Evropa, – jih Střed USA, jihovýchodní Asie, Spojené království – Jih, Spojené království – Západ, East2 USA, západ USA, Virginia verze pro státní správu USA a západní Evropa. Viz [Aktuální omezení pro P11–P15](../articles/sql-database/sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 
>\*\* Maximální počet jednotek Dtu na databázi na 200 Dtu a vyšší počínaje Standard jsou ve verzi preview.
>
