---
title: Přehled provozní kontinuity pomocí Azure Database for PostgreSQL-flexibilního serveru
description: Přečtěte si o konceptech kontinuity podnikových aplikací pomocí Azure Database for PostgreSQL-flexibilního serveru.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 264bb8c66510c90fecf12d2e4e68bd969b4fb474
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90935790"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---flexible-server"></a>Přehled provozní kontinuity pomocí Azure Database for PostgreSQL-flexibilního serveru

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

**Kontinuita podnikových** procesů v Azure Database for PostgreSQL flexibilním serveru se rozumí mechanismům, zásadám a postupům, které umožní vašemu podniku pokračovat v práci na straně přerušení, zejména pro výpočetní infrastrukturu. Ve většině případů se flexibilní Server bude zpracovávat rušivé události, které se můžou vyskytnout v cloudovém prostředí a udržovat aplikace a obchodní procesy spuštěné. Existují však některé události, které nelze zpracovat automaticky, například:

- Uživatel omylem odstraní nebo aktualizuje řádek v tabulce.
- Zemětřesení způsobí výpadek napájení a dočasné zakázání datového centra nebo zóny dostupnosti.
- Oprava databáze je nutná k opravě chyby nebo problému se zabezpečením.

Flexibilní Server nabízí funkce, které chrání data a zmírnit výpadky důležitých databází v případě plánovaných a neplánovaných událostí výpadku. Je postaven na infrastruktuře Azure, která už nabízí robustní odolnost a dostupnost, flexibilní Server má funkce pro provozní kontinuitu, které poskytují další ochranu proti chybám, vyřeší požadavky na dobu obnovení a snižují riziko ztráty dat. Při sestavování aplikací byste měli zvážit toleranci výpadků, což je plánovaná doba obnovení (RTO) a riziko ztráty dat, což je cíl bodu obnovení (RPO). Například vaše podniková databáze vyžaduje mnohem přísnější požadavky na provozuschopnost v porovnání s testovací databází.  

> [!IMPORTANT]
> Doba provozu% smlouvy o úrovni služeb (SLA) není nabízena během verze Preview. 

Následující tabulka znázorňuje funkce flexibilní nabídky serveru.


| **Funkce** | **Popis** | **Důležité informace** |
| ---------- | ----------- | ------------ |
| **Automatické zálohování** | Flexibilní server automaticky provádí každodenní zálohování souborů databáze a průběžně zálohuje transakční protokoly. Zálohy se můžou uchovávat 7 dní až po 35 dnů. Databázový server budete moct obnovit do libovolného bodu v čase v rámci doby uchovávání záloh. RTO závisí na velikosti dat, která mají být obnovena, a na čase k provedení obnovení protokolu. Může to trvat několik minut až 12 hodin. Další podrobnosti najdete v tématu [Koncepty – zálohování a obnovení](./concepts-backup-restore.md). |Zálohovaná data zůstanou v rámci této oblasti. |
| **Redundantní vysoká dostupnost zóny** | Flexibilní Server se dá nasadit s konfigurací zóny s redundantními vysokou dostupností (HA), kde se primární a pohotovostní servery nasazují ve dvou různých zónách dostupnosti v rámci jedné oblasti. Tato konfigurace HA chrání vaše databáze před selháním na úrovni zóny a také pomáhá snižovat prostoje aplikací během plánovaných a neplánovaných výpadků. Data z primárního serveru se replikují do pohotovostní repliky v synchronním režimu. V případě výpadku primárního serveru dojde k automatickému převzetí služeb při selhání repliky serveru. RTO ve většině případů by měla být v rámci 60 s-120s. Očekává se, že RPO má hodnotu nula (bez ztráty dat). Další informace najdete v tématu věnovaném [konceptům a vysoké dostupnosti](./concepts-high-availability.md). | Podporováno pro výpočetní úrovně pro obecné účely a paměťově optimalizované. K dispozici pouze v oblastech, kde je k dispozici více zón. |
| **Disky spravované na úrovni Premium** | Soubory databáze se ukládají do vysoce trvalého a spolehlivého úložiště spravovaného na úrovni Premium. Tím se zajistí redundance dat se třemi kopiemi repliky uloženými v zóně dostupnosti s využitím automatických možností obnovení dat. Další informace najdete v [dokumentaci ke službě Managed disks](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview). | Data uložená v rámci zóny dostupnosti. |
| **Redundantní zálohování zóny** | Flexibilní zálohy serveru jsou automaticky a bezpečně uložené v zóně redundantním úložišti v rámci oblasti. Během selhání na úrovni zóny, kde se zřídí váš server, a pokud váš server není nakonfigurovaný s redundancí zóny, můžete databázi obnovit pomocí nejnovějšího bodu obnovení v jiné zóně. Další informace najdete v tématu [Koncepty – zálohování a obnovení](./concepts-backup-restore.md).| Dá se použít jenom v oblastech, kde je k dispozici více zón.|


## <a name="planned-downtime-events"></a>Události plánovaného výpadku
Níže jsou uvedené některé plánované scénáře údržby. Tyto události obvykle účtují až několik minut výpadků a bez ztráty dat.

| **Scénář** | **Proces**|
| ------------------- | ----------- | 
| <b>Škálování na výpočetní výkon (iniciované uživatelem)| Během operace škálování výpočetních prostředků je povoleno dokončit aktivní kontrolní body, připojení klientů se vyprazdňuje, všechny nepotvrzené transakce se zruší, úložiště se odpojí a pak se vypne. K novému flexibilnímu serveru se stejným názvem databázového serveru se zřídí konfigurace COMPUTE s možností horizontálního navýšení kapacity. Úložiště se pak připojí k novému serveru a spustí se databáze, která v případě potřeby provede obnovení do přijetí připojení klienta. |
| <b>Škálování úložiště (iniciované uživatelem) | Když se iniciuje operace škálování úložiště, aktivní kontrolní body se můžou dokončit, připojení klientů se vyprázdní a všechny nepotvrzené transakce se zruší a pak se vypnou. Velikost úložiště se škáluje na požadovanou velikost a pak se připojí k novému serveru. V případě potřeby se provede obnovení před přijetím připojení klienta. Počítejte s tím, že škálování velikosti úložiště se nepodporuje. |
| <b>Nové nasazení softwaru (iniciované v Azure) | Nové opravy funkcí nebo opravy chyb se automaticky nastávají v rámci plánované údržby služby a můžete naplánovat, kdy se tyto aktivity budou provádět. Další informace najdete na [portálu](https://aka.ms/servicehealthpm). | 
| <b>Inovace dílčí verze (iniciované v Azure) | Azure Database for PostgreSQL automaticky opraví databázové servery na podverzi určenou v Azure. K tomu dochází jako součást plánované údržby služby. Databázový server se automaticky restartuje s novou dílčí verzí. Další informace najdete v [dokumentaci](https://docs.microsoft.com/azure/postgresql/concepts-monitoring#planned-maintenance-notification). Můžete také kontrolovat [portál](https://aka.ms/servicehealthpm).| 

 Pokud je flexibilní server nakonfigurovaný se **záložní vysokou dostupností zóny**, flexibilní Server provede nejprve škálování a operace údržby na pohotovostním serveru. Další informace najdete v tématu věnovaném [konceptům a vysoké dostupnosti](./concepts-high-availability.md).

##  <a name="unplanned-downtime-mitigation"></a>Zmírnění neplánovaných výpadků

Neplánované výpadky se můžou vyskytnout v důsledku nepředvídatelného přerušení, jako je základní hardwarová chyba, problémy se sítí a softwarové chyby. Pokud dojde k neočekávanému výpadku databázového serveru s vysokou dostupností, bude se aktivovat pohotovostní replika a klienti budou moci pokračovat v práci. Pokud není nakonfigurovaný s vysokou dostupností (HA), pak se automaticky zřídí nový databázový server. I když nemůžete se vyhnout neplánovanému výpadku, flexibilní Server pomáhá zmírnit výpadky tím, že automaticky provádí operace obnovení bez nutnosti zásahu člověka. 
   
### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Neplánované výpadky: scénáře selhání a obnovení služby
Níže jsou uvedeny některé neplánované scénáře selhání a proces obnovení. 

| **Scénář** | **Proces obnovení [ne HA]** | **Proces obnovení [HA]** |
| ---------- | ---------- | ------- |
| <B>Selhání databázového serveru | Pokud je databázový server mimo provoz, Azure se pokusí restartovat databázový server. Pokud se to nezdaří, databázový server se restartuje na jiném fyzickém uzlu.  <br /> <br /> Doba obnovení (RTO) závisí na různých faktorech, včetně aktivity v době selhání, jako je například Velká transakce a množství obnovení, které se má provést během procesu spuštění databázového serveru. <br /> <br /> Aplikace, které používají databáze PostgreSQL, musí být sestaveny způsobem, který detekuje a znovu Zahozená připojení a neúspěšné transakce. | Pokud se zjistí selhání databázového serveru, server se převezme na pohotovostní server, čímž se sníží prostoje. Další informace najdete na [stránce koncepty vysoké dostupnosti](../concepts-high-availability.md). Očekává se, že RTO bude 60-120s s nulovou ztrátou dat. |
| <B>Selhání úložiště | Aplikace nevidí žádný dopad na jakékoli problémy související s úložištěm, jako je selhání disku nebo poškození fyzického bloku. Jelikož jsou data uložena ve třech kopiích, kopie dat je obsluhována zbývajícím úložištěm. Poškozený blok dat je automaticky opraven a automaticky se vytvoří nová kopie dat. | U všech vzácných a neobnovitelných chyb, jako je například celé úložiště, je nepřístupný k serveru flexibilní repliky, aby se snížil výpadek. Další informace najdete na [stránce koncepty vysoké dostupnosti](../concepts-high-availability.md). |
| <b> Chyby logických/uživatelských uživatelů | Chcete-li provést obnovení z uživatelských chyb, například omylem vyřazených tabulek nebo nesprávně aktualizovaných dat, je nutné provést obnovení k určitému [bodu v čase](https://docs.microsoft.com/azure/postgresql/concepts-backup) (PITR). Při provádění operace obnovení zadáte vlastní bod obnovení, což je čas, který je přímo předtím, než došlo k chybě.<br> <br>  Chcete-li obnovit pouze podmnožinu databází nebo konkrétních tabulek a nikoli všechny databáze na databázovém serveru, můžete obnovit databázový server v nové instanci, exportovat tyto tabulky prostřednictvím [pg_dump](https://www.postgresql.org/docs/11/app-pgdump.html)a potom pomocí [pg_restore](https://www.postgresql.org/docs/11/app-pgrestore.html) obnovit tyto tabulky do databáze. | Tyto chyby uživatelů nejsou chráněny s vysokou dostupností, protože všechny změny jsou replikovány do záložní repliky synchronně. K zotavení z takových chyb je nutné provést obnovení k bodu v čase. |
| <b> Selhání zóny dostupnosti | Chcete-li provést obnovení při selhání na úrovni zóny, můžete provést obnovení k určitému bodu v čase pomocí zálohování a vybrat vlastní bod obnovení s nejnovějším časem obnovení nejnovějších dat. Nový flexibilní Server se nasadí v jiné neovlivněné zóně. Doba potřebná k obnovení závisí na předchozí záloze a na objemu transakčních protokolů, které se mají obnovit. | Flexibilní Server se automaticky převezme na pohotovostní server v rámci 60 120s s nulovou ztrátou dat. Další informace najdete na [stránce koncepty vysoké dostupnosti](../concepts-high-availability.md). | 
| <b> Selhání oblasti | Replika čtení mezi oblastmi a geografické obnovení zálohovacích funkcí se ještě ve verzi Preview nepodporují. | |


> [!IMPORTANT]
> Odstraněné servery **nelze**   obnovit. Pokud server odstraníte, odstraní se i všechny databáze patřící do serveru a nebude možné je obnovit. Pomocí [zámku prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)můžete   zabránit nechtěnému odstranění serveru.


## <a name="next-steps"></a>Další kroky

-   Přečtěte si o [vysoké dostupnosti zóny jako redundantní](./concepts-high-availability.md) .
-   Další informace o [zálohování a obnovení](./concepts-backup-restore.md)
