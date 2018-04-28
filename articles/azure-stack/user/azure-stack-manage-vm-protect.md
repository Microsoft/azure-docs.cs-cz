---
title: Ochrana virtuálních počítačů nasadit v Azure zásobníku | Microsoft Docs
description: Pokyny k ochraně virtuálních počítačů nasazených v zásobníku Azure.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/25/2018
ms.author: jeffgilb
ms.reviewer: hector.linares
ms.openlocfilehash: b49a8650611472b5e35c4bdf8373a1d7e3a45589
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="protect-virtual-machines-deployed-on-azure-stack"></a>Ochrana virtuálních počítačů nasazených v Azure zásobníku
Tento článek popisuje pokyny o tom, jak chránit virtuální počítače uživatele nasazené v Azure zásobníku.

K ochraně proti ztrátě informací a neplánované výpadky, potřebujete implementovat plán zálohování obnovení nebo zotavení po havárii pro vaše uživatele aplikace a data. Tento plán je jedinečný pro každou aplikaci, ale následuje rozhraní podle vaší organizace komplexní provozní kontinuitu a strategii (BC/DR) zotavení po havárii. Obecné vzory a postupy pro aplikaci dostupnosti a odolnosti najdete v části [návrh odolný aplikací pro Azure](https://docs.microsoft.com/azure/architecture/resiliency) architektura centra Azure.

## <a name="azure-stack-infrastructure-recovery"></a>Obnovení infrastruktury Azure zásobníku

Uživatelé budou muset samostatně chránit jejich virtuální počítače ze služby infrastruktury Azure zásobníku.

Pokud zásobník Azure cloud už je v režimu offline delší dobu nebo trvale neopravitelné, musíte mít plán na místě, aby vaše aplikace vyřízení požadavků uživatele s minimálními výpadky. Operátor cloudu zásobník Azure zodpovídá za plán obnovení Příprava základní infrastruktury Azure zásobníku a služby. Další informace najdete v článku [zotavit závažné ztráty dat](https://docs.microsoft.com/azure/azure-stack/azure-stack-backup-recover-data). 

Plán obnovení pro služby infrastruktury Azure zásobníku nezahrnuje obnovení uživatele virtuální počítače, účty úložiště nebo databáze. Jako vlastník aplikace jste zodpovědní za implementaci plán obnovení pro vaše aplikace a data.
 
## <a name="sourcetarget-combinations"></a>Zdroj a cíl kombinace

Každý cloud Azure zásobníku se nasadí do jednoho datového centra. Samostatné prostředí je požadovaná, takže můžete obnovit vaše aplikace. V prostředí může být samostatné cloudu Azure zásobníku v jiném datovém centru nebo veřejného cloudu Azure. Vaše požadavky na data o ochraně osobních údajů a suverenity dat. určí obnovení prostředí pro vaši aplikaci. Jako povolíte ochranu pro každou aplikaci, máte možnost zvolit možnost konkrétní obnovení pro každé z nich. Aplikace může mít v rámci jednoho předplatného zálohování dat k jinému datovému centru. V jiné předplatné můžete replikaci dat do veřejného cloudu Azure. 
 
Plánování strategie zálohování obnovení a zotavení po havárii pro každou aplikaci k určení cíle pro každou aplikaci. To vám pomůže vaší organizace správně velikost kapacity úložiště vyžaduje místní a projektu spotřebu ve veřejném cloudu. 

|  | Globální Azure | Azure zásobník nasazený do datacentru CSP a provozují zprostředkovatele kryptografických služeb | Azure zásobník nasazený do datového centra zákazníka a provozují zákazníka |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **Azure zásobník nasazený do datacentru CSP a provozují zprostředkovatele kryptografických služeb** | Virtuální počítače uživatele se nasadí do zásobníku Azure CSP zpracovat. Virtuální počítače uživatele jsou obnovena ze zálohy nebo převzetí služeb při selhání přímo do Azure. | Zprostředkovatel kryptografických služeb funguje primární a sekundární instance zásobník Azure ve svých vlastních datových centrech. Virtuální počítače uživatele jsou obnovena nebo převzetí služeb při selhání mezi dvěma instancemi Azure zásobníku. | Zprostředkovatel kryptografických služeb pracuje Azure zásobníku v primární lokalitě. Datovým centrem zákazníka je cílem obnovení nebo převzetí služeb při selhání. |
| **Azure zásobník nasazený do datového centra zákazníka a provozují zákazníka** | Virtuální počítače uživatele se nasadí do zákazník provozovat zásobník Azure. Virtuální počítače uživatele jsou obnovena ze zálohy nebo převzetí služeb při selhání přímo do Azure. | Zákazník funguje primární a sekundární instance zásobník Azure ve svých vlastních datových centrech. Virtuální počítače uživatele jsou obnovena nebo převzetí služeb při selhání mezi dvěma instancemi Azure zásobníku. | Zákazník funguje Azure zásobníku v primární lokalitě. Datacentru CSP na je cílem obnovení nebo převzetí služeb při selhání. |

![Kombinace cílového zdroje](media\azure-stack-manage-vm-backup\vm_backupdataflow_01.png)

## <a name="application-recovery-objectives"></a>Cíle obnovení aplikací

Musíte určit dobu výpadku a ztrátu dat, které vaše organizace může tolerovat pro každou aplikaci, určit nejlepší metodou k ochraně aplikací v Azure zásobníku. Množství downtown a množství hodnot ztráty dat jsou nezbytné k identifikaci Chcete-li vytvořit a implementovat plán obnovení, který minimalizuje dopad pro vaši organizaci. Pro každou aplikaci vezměte v úvahu:  
 - **Cíli času obnovení (RTO)**  
RTO je maximální přijatelnou dobu, kterou aplikace může být k dispozici po incidentu. Pokud stanovíte RTO na 90 minut, musíte být schopni obnovit aplikaci do běžícího stavu do 90 minut od začátku výpadku. Pokud máte nízkou RTO, může zachovat druhé nasazení neustále spuštěna v úsporném režimu pro ochranu proti místní výpadku.
 - **Cíl bodu obnovení (RPO)**  
Plánovaný bod obnovení je maximální délka trvání dojít ke ztrátě dat, které je přijatelné při havárii. Například pokud ukládáte data do jedné databáze, nereplikujete je do jiné databáze a provádíte zálohování každou hodinu, mohlo by dojít ke až ztrátě dat uložených za poslední hodinu.
 
Plánovaná doba obnovení a cíl bodu obnovení jsou obchodní požadavky. Posouzení rizika, můžete definovat RTO aplikace a plánovaný bod obnovení. Další běžné metrika **střední čas k obnovení** (MTTR), což je průměrná doba, která je potřebná pro obnovení po selhání aplikace. MTTR je empirický fakt o systému. Pokud MTTR překročí RTO, pak selhání v systému způsobí nepřijatelné narušení činnosti firmy, protože nebude možné obnovit systém v definovaném čase RTO.

### <a name="backup-restore"></a>Obnovit zálohování

Nejběžnější schéma ochrany pro aplikace založené na virtuální počítač je použít zálohovací software. Zálohování virtuálních počítačů obvykle zahrnuje operačního systému, konfigurace operačního systému, binární soubory aplikace a data aplikací. Zálohy jsou vytvořené pomocí pořízení snímku svazky, disky nebo celý virtuální počítač. S Azure zásobníku máte flexibilitu zálohování z v kontextu hostovaný operační systém nebo z Azure zásobníku úložiště a výpočetní rozhraní API. Azure zásobník nepodporuje pořízení zálohy na úrovni hypervisoru. 
 
![Zálohování restor](media\azure-stack-manage-vm-backup\vm_backupdataflow_03.png)
 
Obnovení aplikace vyžaduje obnovení jeden nebo více virtuálních počítačů na stejném cloudu nebo do nového cloudu. Ve vašem datovém centru nebo veřejného cloudu, můžete vybrat v cloudu. Které cloudové cílíte je zcela v rámci vlastního ovládacího prvku a je založena na požadavky na ochranu osobních údajů a suverenity vaše data. 
 
 - RTO: Výpadek měřeno v hodinách 
 - Plánovaný bod obnovení: Proměnné datové ztráty (v závislosti na četnost zálohování)
 - Topologie nasazení: aktivní/pasivní 

#### <a name="planning-your-backup-strategy"></a>Plánování strategie zálohování

Plánování strategie zálohování a definování požadavků škálování začíná kvantifikace se počet instancí virtuálních počítačů, které musí být chráněny. Zálohování všechny virtuální počítače na všechny servery v prostředí, je velmi běžné strategie. S Azure zásobníku, existují však některé virtuální počítače, které mají být zálohovány. Virtuální počítače ve škálovací sadě například jsou považovány za dočasných prostředků, které může pocházet a přejděte někdy bez předchozího upozornění. Žádná trvanlivý data, která je potřeba chránit je uložené v samostatné úložiště jako databáze nebo objekt úložiště. Důležité informace pro zálohování virtuálních počítačů v Azure zásobníku:

 - **Kategorizaci**
    - Vezměte v úvahu modelu, kde uživatelé vyjádřit výslovný souhlas pro zálohování virtuálních počítačů.
    - Definujte obnovení, které smlouvy o úrovni služeb na základě priority aplikace nebo dopad na firmu.
 - **Škálování**
    - Při Startovní velký počet nové virtuální počítače (Pokud je nutné zálohování), zvažte postupný zálohy.
    - Vyhodnoťte zálohování produkty, které můžete efektivně sběr a přenos zálohovaná data chcete-li minimalizovat obsah prostředku v řešení.
    - Vyhodnoťte zálohování produkty, které efektivně ukládat zálohovaná data pomocí přírůstkové nebo rozdílové zálohy. Chcete-li minimalizovat nutnost úplných záloh pro vyžádání obsahu mezi všechny virtuální počítače v prostředí.
 - **Obnovení**
    - Zálohování produktů můžete obnovit virtuální disky, data aplikací v rámci stávajícího virtuálního počítače, nebo celý prostředků virtuálního počítače a přidruženými virtuálními disky. Schéma obnovení, které je třeba závisí na tom, jak v plánu obnovení aplikace a bude mít vliv na vaše dobu obnovení aplikace. Například může být snazší k nasazení systému SQL server ze šablony a potom obnovte databáze místo obnovení celý virtuální počítač nebo sadu virtuálních počítačů.


### <a name="replicationmanual-failover"></a>Replikace nebo ruční převzetí služeb při selhání

Alternativní způsob podpora vysoké dostupnosti je replikace virtuálních počítačů vaší aplikace do jiného cloudu a spoléhají na ručně spouštěná převzetí služeb při selhání. Replikace operačního systému, binární soubory aplikace a data aplikací můžete provést na úrovni virtuálního počítače nebo na úrovni operačního systému hosta. Správa převzetí služeb při selhání pomocí další software, které jsou oddělené od aplikace.
 
S tímto přístupem je aplikace nasazená v jedné cloudu. Virtuální počítač se pak replikují do jiných cloudu v jedné vaší datových center nebo veřejného cloudu. Pokud se aktivuje převzetí služeb při selhání, sekundární virtuální počítače budou muset zapnutý v druhé cloudu. V některých případech převzetí služeb při selhání se virtuální počítače a vytvořte na disky. Tento proces může trvat delší dobu dobu trvat, zejména s víceúrovňových aplikací s konkrétní spuštění pořadí. Mohou existovat další kroky, které je třeba spustit před aplikace můžete začít požadavky obsluhy.

![Replikace ruční převzetí služeb při selhání](media\azure-stack-manage-vm-backup\vm_backupdataflow_02.png)

 - RTO: Výpadek měřená v minutách 
 - Plánovaný bod obnovení: Proměnné datové ztráty (podle četnosti replikací)
 - Topologie nasazení: úsporný režim s připojením aktivní/pasivní
 
### <a name="high-availabilityautomatic-failover"></a>Vysoká dostupnost automatické převzetí služeb při selhání

Pro aplikace, kde vaše business tolerovat pár sekund nebo minut výpadky a minimálními ztrátami dat musíte vzít v úvahu konfigurace s vysokou dostupností. Vysoká dostupnost aplikací jsou navrženy pro rychle a automaticky zotavit chyb. Pro místní hardware chyb implementuje infrastruktury Azure zásobníku vysoké dostupnosti ve fyzické sítě pomocí dvou horní části rack přepínače. Pro výpočty úrovni chyb zásobník Azure používá více uzlů v jednotce škálování. Na úrovni virtuálního počítače můžete sady škálování v kombinaci s domén selhání zajistěte, aby selhání uzlu nepřebírají vaší aplikace. 
 
V kombinaci s sady škálování vaše aplikace bude muset nativně podporují vysokou dostupnost nebo podporují použití clusteringu softwaru. Například Microsoft SQL Server podporuje vysokou dostupnost nativně pro databáze, které používají režim synchronního potvrzování. Ale pokud podporuje pouze asynchronní replikaci, pak bude ztrátu dat. Také mohou být aplikace nasazeny do clusteru s podporou převzetí služeb při selhání kde software clusteru zpracovává automatické převzetí služeb při selhání aplikace. 
 
Použití tohoto přístupu, aplikaci je aktivní pouze v jedné cloudu, ale bude software nasazen do víc cloudů. Ostatních cloudů jsou v úsporném režimu připraven ke spuštění aplikace, když se aktivuje převzetí služeb při selhání.

 - RTO: Výpadek měří v sekundách 
 - Plánovaný bod obnovení: Minimálními ztrátami dat
 - Topologie nasazení: úsporný režim s připojením aktivní/aktivní

### <a name="fault-tolerance"></a>Odolnost proti chybám

Azure zásobníku fyzické redundance a infrastruktury dostupnost služeb chránit pouze na hardwarové úrovni chyb jako poškozený disk, se nezdařilo napájení, port sítě nebo nezdařilo. uzel. Ale pokud vaše aplikace musí být k dispozici a nikdy ke ztrátě dat., musíte implementovat odolnost proti chybám nativně v aplikaci nebo zapnout odolnost proti chybám s další software. 
 
Nejdřív je potřeba zajistit, že aplikace, které virtuální počítače jsou nasazeny pomocí škálování nastaví na ochranu proti selhání uzlu úrovni. K ochraně proti cloudu přechod do stavu offline, stejnou aplikaci musí již být nasazeny na jiný Cloud, tak může pokračovat zpracování požadavků bez přerušení. To se obvykle označuje na aktivní aktivní nasazení.
 
Uvědomte si, že každý zásobník Azure cloud nachází nezávisle na sobě navzájem, proto cloudy jsou považováno za aktivní z hlediska služby infrastruktury. V takovém případě více aktivních instancích aplikace se nasadí do minimálně jeden cloud aktivní. 
 
 - RTO: Bez výpadků
 - Plánovaný bod obnovení: Nedošlo ke ztrátě dat
 - Topologie nasazení: aktivní/aktivní

### <a name="no-recovery"></a>Žádné obnovení

Ochrana proti neplánované výpadky nebo ztrátě dat nemusí být nutné některé aplikace ve vašem prostředí. Například virtuální počítače použít pro vývoj a testování obvykle není nutné obnovit. Je rozhodnout a to bez ochranu pro aplikace nebo konkrétní virtuální počítač. Azure zásobníku nenabízí od základní infrastruktury zálohování nebo replikace virtuálních počítačů. Podobně jako u Azure, se musíte přihlásit k ochrany pro každý virtuální počítač v každé z vašich předplatných.  
 
 - RTO: neopravitelné 
 - Plánovaný bod obnovení: Úplné ztrátě dat
 
## <a name="recommended-topologies"></a>Doporučená topologie 

Důležité informace pro vaše nasazení Azure zásobníku:

|     | Doporučení | Komentáře |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Zálohování nebo obnovení virtuálních počítačů na externí cíle zálohy, která už nasazená ve vašem datovém centru | Doporučené | Využijte stávající infrastrukturu pro zálohování a provozní znalosti. Ujistěte se, že velikost infrastruktury zálohování tak, aby byl připraven k ochraně další instance virtuálních počítačů. Zajistěte, aby infrastruktury zálohování není v těsné blízkosti svůj zdroj. Virtuální počítače můžete obnovit zdroj zásobník Azure sekundární instance zásobník Azure nebo Azure. |
| Zálohování a obnovení virtuálních počítačů externí zálohování cíl vyhrazený pro Azure zásobníku | Doporučené | Nové infrastruktury zálohování nebo vyhrazená infrastruktura zálohování zřizování si můžete zakoupit pro Azure zásobníku. Zajistěte, aby infrastruktury zálohování není v těsné blízkosti svůj zdroj. Virtuální počítače můžete obnovit zdroj zásobník Azure sekundární instance zásobník Azure nebo Azure. |
| Zálohování a obnovení virtuálních počítačů přímo do globální Azure nebo poskytovatele důvěryhodné služby | Doporučené | Také můžete splňovat zákonné požadavky a ochrany osobních údajů, můžete uložit záloh v globální Azure nebo poskytovatele služeb důvěryhodné. V ideálním případě poskytovatele služeb také běží zásobník Azure tak, abyste dosáhli konzistence v provozní prostředí při obnovení. |
| Virtuální počítače nebo replikace převzetí služeb při selhání do jiné instance Azure zásobníku | Doporučené | V případě převzetí služeb při selhání musíte mít druhý zásobník Azure cloud plně funkční, se můžete vyhnout výpadky rozšířené aplikací. |
| Replikovat nebo převzetí služeb při selhání virtuálních počítačů přímo do Azure nebo poskytovatele služeb důvěryhodné služby | Doporučené | Také můžete splňovat zákonné požadavky a ochrany osobních údajů, můžete replikovat data globální Azure nebo poskytovatele služeb důvěryhodné. V ideálním případě poskytovatele služeb také běží zásobník Azure tak, abyste dosáhli konzistence v provozní prostředí po převzetí služeb při selhání. |
| Nasazení cíl zálohování na stejném cloudu Azure zásobníku se data aplikací | Nedoporučuje se | Vyhněte se ukládání záloh na stejném cloudu Azure zásobníku. Neplánovaný výpadek cloudu si můžete udržovat z primární data a data záloh. Pokud se rozhodnete nasadit cíl zálohy jako virtuální zařízení (pro účely optimalizace pro zálohování a obnovení), ujistěte se, všechna data nepřetržitě zkopírován do externího umístění zálohy. |
| Nasadit fyzické zálohování zařízení do stejné rack, kde je nainstalován zásobník Azure řešení | Nepodporuje se | V tuto chvíli nemůžete se připojit jiná zařízení do horní části rack přepínače, které nejsou součástí v původním řešení. |
 
## <a name="next-steps"></a>Další postup 

V tomto článku jsme zahrnutých pokyny o tom, jak chránit virtuální počítače uživatele nasazené v Azure zásobníku. Další informace o tom, jak chránit virtuální počítače pomocí služeb Azure najdete v části:
 - [Zálohování souborů a aplikací v zásobníku Azure pomocí Azure Backup](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Azure Backup Server podpora pro Azure zásobníku](https://docs.microsoft.com/azure/backup/ ) 
 - [Azure Site Recovery podpora pro Azure zásobníku](https://docs.microsoft.com/azure/site-recovery/)  
 
Další informace o partnera produkty, které nabízejí ochranu virtuálních počítačů v zásobníku Azure, najdete v tématu "[chrání aplikace a data v Azure zásobníku](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/)."
