---
title: Popis zahrnutého souboru: zahrnout souborové služby: Event-Centre Author: spelluru MS. Service: Event-Centers MS. téma: include MS. Date: 02/01/2021 MS. Author: spelluru MS. Custom: "include File", "FastTrack-Edit", "IoT", "Event-hub"

---

Následující tabulky poskytují kvóty a omezení specifické pro [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Informace o cenách Event Hubs najdete v článku [Event Hubs ceny](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="common-limits-for-all-tiers"></a>Společná omezení pro všechny úrovně
Následující omezení jsou společná napříč všemi úrovněmi. 

| Omezení |  Poznámky | Hodnota |
| --- |  --- | --- |
| Počet Event Hubs oborů názvů na předplatné |- |100 |
| Počet Center událostí na obor názvů | Následné žádosti o vytvoření nového centra událostí budou odmítnuty. |10 |
| Velikost názvu centra událostí |- | 256 znaků |
| Velikost názvu skupiny uživatelů |- | 256 znaků |
| Počet neepochach přijímačů na skupinu uživatelů |- |5 |
| Počet autorizačních pravidel na obor názvů | Následné žádosti o vytvoření autorizačního pravidla jsou odmítnuté.|12 |
| Počet volání metody Getruntimeinformation – |  - | 50 za sekundu | 
| Počet virtuálních sítí (VNet) | - | 128 | 
| Počet pravidel konfigurace protokolu IP | - | 128 | 


### <a name="basic-vs-standard-tiers"></a>Úrovně Basic a Standard
V následující tabulce jsou uvedena omezení, která se mohou lišit pro úrovně Basic a Standard. 

| Omezení | Poznámky | Basic | Standard |
|---|---|--|---|
| Maximální velikost publikace Event Hubs| &nbsp; | 256 kB | 1 MB |
| Počet skupin uživatelů na centrum událostí | &nbsp; |1 |20 |
| Počet připojení AMQP na obor názvů | Následné žádosti o další připojení jsou odmítnuty a volající kód obdrží výjimku. |100 |5 000|
| Maximální doba uchování dat události | &nbsp; |1 den |1-7 dní |
| Maximální počet jednotek propustnosti |Překročení tohoto limitu způsobí omezení vašich dat a vygeneruje [výjimku zaneprázdněnou serverem](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Pokud chcete požádat o větší počet jednotek propustnosti pro úroveň Standard, zaregistrujte [žádost o podporu](../articles/azure-portal/supportability/how-to-create-azure-support-request.md). [Další jednotky propustnosti](../articles/event-hubs/event-hubs-auto-inflate.md) jsou k dispozici v blocích po 20 na potvrzeném nákupu. |20 | 20 | 
| Počet oddílů na centrum událostí | |32 | 32 | 

> [!NOTE]
>
> Události můžete publikovat jednotlivě nebo v dávce. 
> Limit publikace (podle SKU) platí bez ohledu na to, zda se jedná o jednu událost nebo dávku. Publikování událostí větších, než je maximální prahová hodnota, bude odmítnuto.

### <a name="dedicated-tier-vs-standard-tier"></a>Vyhrazená úroveň vs. úroveň Standard
V nabídce Event Hubs úrovně Dedicated se účtuje pevná měsíční cena s minimálním počtem 4 hodin využití. Vyhrazená úroveň nabízí všechny funkce plánu Standard, ale kapacitu a omezení na úrovni podniku pro zákazníky s náročnými úlohami. 

V tomto [dokumentu](../articles/event-hubs/event-hubs-dedicated-cluster-create-portal.md) najdete informace o tom, jak vytvořit vyhrazený cluster Event Hubs pomocí Azure Portal.

| Funkce | Standard | Vyhrazená |
| --- |:---|:---|
| Šířka pásma | 20 počet propustnosti (až 40 počet propustnosti) | 20 kapacitní jednotky |
| Obory názvů |  1 | 50 za CU |
| Event Hubs |  10 na obor názvů | 1000 na obor názvů |
| Události příchozího přenosu dat | Plaťte za milion událostí | Zahrnuje |
| Velikost zprávy | 1 000 000 bajtů | 1 000 000 bajtů |
| Oddíly | 32 na centrum událostí | 1024 na centrum událostí<br/>2000 za CU |
| Skupiny uživatelů | 20 na centrum událostí | Bez omezení na CU, 1000 na každé centrum událostí |
| Zprostředkovaná připojení | 1 000 zahrnutý, 5 000 max | 100 K zahrnutí a maximum |
| Uchovávání zpráv | 7 dní, 84 GB zahrnutých za TU | 90 dní, zahrnutých 10 TB za CU |
| Zachytávání | Platba za hodinu | Zahrnuje |


### <a name="schema-registry-limitations"></a>Omezení registru schématu

#### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>Omezení, která jsou stejná pro standardní a vyhrazené úrovně 
| Příznak | Omezení | 
|---|---|
| Maximální délka názvu skupiny schémat | 50 |  
| Maximální délka názvu schématu | 100 |    
| Velikost v bajtech na schéma | 1 MB |   
| Počet vlastností na skupinu schémat | 1024 |
| Velikost v bajtech na klíč vlastnosti skupiny | 256 | 
| Velikost v bajtech na hodnotu vlastnosti skupiny | 1024 | 


#### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>Omezení, která jsou odlišná na úrovni Standard a vyhrazené 

| Omezení | Standard | Vyhrazená | 
|---|---|--|
| Velikost registru schématu (obor názvů) v megabajtů bajtech | 25 |  1024 |
| Počet skupin schémat v registru schématu nebo oboru názvů | 1 – vyloučení výchozí skupiny | 1000 |
| Počet verzí schématu napříč všemi skupinami schémat | 25 | 10000 |