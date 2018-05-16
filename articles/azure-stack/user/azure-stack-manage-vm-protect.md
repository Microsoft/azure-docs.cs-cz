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
ms.date: 05/14/2018
ms.author: jeffgilb
ms.reviewer: hector.linares
ms.openlocfilehash: 734ee0e6ffb0dab660a2b63b431780208e0e0484
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="protect-virtual-machines-deployed-on-azure-stack"></a>Ochrana virtuálních počítačů nasazených v Azure zásobníku

Použijte tento článek jako vodítko k vývoji plán ochranu virtuálních počítačů (VM), které uživatelům nasadit v Azure zásobníku.

K ochraně proti ztrátě informací a neplánované výpadky, potřebujete implementovat plán zálohování obnovení nebo zotavení po havárii pro uživatelské aplikace a jejich data. Tento plán může být jedinečný pro každou aplikaci, ale následuje rozhraní podle vaší organizace komplexní provozní kontinuitu a strategii (BC/DR) zotavení po havárii. Je to dobrý výchozí bod [návrh odolný aplikací pro Azure](https://docs.microsoft.com/azure/architecture/resiliency), který poskytuje obecné vzory a postupy pro aplikace dostupnost a odolnost.

>[!IMPORTANT]
> Testovací plány obnovení zálohování a zotavení po havárii průběžně. Musíte zajistit, aby toto:
> * Plány práce
> * Plány stále splňují, které byly navrženy pro potřeby.

## <a name="azure-stack-infrastructure-recovery"></a>Obnovení infrastruktury Azure zásobníku

Uživatelé odpovědni za ochranu jejich virtuální počítače odděleně od služby infrastruktury Azure zásobníku.

Plán obnovení pro služby infrastruktury Azure zásobníku **nemá** zahrnují virtuální počítače uživatele, účty úložiště nebo databáze obnovení. Jako vlastník aplikace jste zodpovědní za implementaci plán obnovení pro vaše aplikace a data.

Pokud zásobník Azure cloud je v režimu offline delší dobu nebo trvale neopravitelné, musíte mít obnovení plánování v umístíte který:

* Zajišťuje minimalizace výpadků
* Zachová kritické virtuální počítače, jako je například databázové servery, systémem
* Umožňuje aplikacím zachovat obsluhy uživatelských požadavků

Operátor cloudu zásobník Azure zodpovídá za vytvoření plánu obnovení pro základní infrastrukturu Azure zásobníku a služby. Další informace najdete v článku [zotavit závažné ztráty dat](https://docs.microsoft.com/azure/azure-stack/azure-stack-backup-recover-data).

## <a name="sourcetarget-combinations"></a>Zdroj a cíl kombinace

Každý cloud Azure zásobníku se nasadí do jednoho datového centra. Samostatné prostředí je požadovaná, takže můžete obnovit vaše aplikace. Obnovení prostředí může být jiného cloudu Azure zásobníku v jiném datovém centru nebo veřejného cloudu Azure. Vaše požadavky na data o ochraně osobních údajů a suverenity dat. určí obnovení prostředí pro vaši aplikaci. Jako povolíte ochranu pro každou aplikaci, máte možnost zvolit možnost konkrétní obnovení pro každé z nich. Aplikace může mít v rámci jednoho předplatného zálohování dat k jinému datovému centru. V jiné předplatné můžete replikaci dat do veřejného cloudu Azure.

Plánování strategie zálohování obnovení a zotavení po havárii pro každou aplikaci k určení cíle pro každou aplikaci. Plán obnovení pomůže organizaci správně velikost úložiště požadovaná kapacita místní a projektu spotřebu ve veřejném cloudu.

|  | Globální Azure | Azure zásobník nasazený do datacentru CSP a provozují zprostředkovatele kryptografických služeb | Azure zásobník nasazený do datového centra zákazníka a provozují zákazníka |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **Azure zásobník nasazený do datacentru CSP a provozují zprostředkovatele kryptografických služeb** | Virtuální počítače uživatele se nasadí do zásobníku Azure CSP zpracovat. Virtuální počítače uživatele jsou obnovena ze zálohy nebo převzetí služeb při selhání přímo do Azure. | Zprostředkovatel kryptografických služeb funguje primární a sekundární instance zásobník Azure ve svých vlastních datových centrech. Virtuální počítače uživatele jsou obnovena nebo převzetí služeb při selhání mezi dvěma instancemi Azure zásobníku. | Zprostředkovatel kryptografických služeb pracuje Azure zásobníku v primární lokalitě. Datovým centrem zákazníka je cílem obnovení nebo převzetí služeb při selhání. |
| **Azure zásobník nasazený do datového centra zákazníka a provozují zákazníka** | Virtuální počítače uživatele se nasadí do zákazník provozovat zásobník Azure. Virtuální počítače uživatele jsou obnovena ze zálohy nebo převzetí služeb při selhání přímo do Azure. | Zákazník funguje primární a sekundární instance zásobník Azure ve svých vlastních datových centrech. Virtuální počítače uživatele jsou obnovena nebo převzetí služeb při selhání mezi dvěma instancemi Azure zásobníku. | Zákazník funguje Azure zásobníku v primární lokalitě. Datacentru CSP na je cílem obnovení nebo převzetí služeb při selhání. |

![Kombinace cílového zdroje](media\azure-stack-manage-vm-backup\vm_backupdataflow_01.png)

## <a name="application-recovery-objectives"></a>Cíle obnovení aplikací

Musíte určit dobu výpadku a ztrátu dat, které vaše organizace může tolerovat pro každou aplikaci. Podle kvantifikace výpadky a ztrátě dat. můžete vytvořit plán obnovení, který minimalizuje dopad po havárii v organizaci. Pro každou aplikaci vezměte v úvahu:

 - **Cíli času obnovení (RTO)**  
RTO je maximální přijatelnou dobu, kterou aplikace může být k dispozici po incidentu. Například RTO 90 minut znamená, že musíte být schopni obnovit použití k běžícímu stavu během 90 minut od začátku po havárii. Pokud máte nízkou RTO, může zachovat druhé nasazení neustále spuštěna v úsporném režimu pro ochranu proti místní výpadku.
 - **Cíl bodu obnovení (RPO)**  
Plánovaný bod obnovení je maximální délka trvání dojít ke ztrátě dat, které je přijatelné při havárii. Například pokud ukládáte data do jedné databáze, nereplikujete je do jiné databáze a provádíte zálohování každou hodinu, mohlo by dojít ke až ztrátě dat uložených za poslední hodinu.

Plánovaná doba obnovení a cíl bodu obnovení jsou obchodní požadavky. Posouzení rizika, můžete definovat RTO aplikace a plánovaný bod obnovení.

Další metrika **střední čas k obnovení** (MTTR), což je průměrná doba, která je potřebná pro obnovení po selhání aplikace. MTTR je základě zkušeností hodnota pro systém. Pokud MTTR překročí RTO, pak selhání v systému způsobí nepřijatelné narušení činnosti firmy, protože nebude možné obnovit systém v definovaném čase RTO.

### <a name="backup-restore"></a>Obnovit zálohování

Nejběžnější schéma ochrany pro aplikace založené na virtuální počítač je použít zálohovací software. Zálohování virtuálních počítačů obvykle zahrnuje operačního systému, konfigurace operačního systému, binární soubory aplikace a data aplikací. Zálohy jsou vytvořené pomocí pořízení snímku svazky, disky nebo celý virtuální počítač. S Azure zásobníku máte flexibilitu zálohování z v kontextu hostovaný operační systém nebo z Azure zásobníku úložiště a výpočetní rozhraní API. Azure zásobník nepodporuje pořízení zálohy na úrovni hypervisoru.
 
![Zálohování restor](media\azure-stack-manage-vm-backup\vm_backupdataflow_03.png)

Obnovení aplikace vyžaduje obnovení jeden nebo více virtuálních počítačů na stejném cloudu nebo do nového cloudu. Ve vašem datovém centru nebo veřejného cloudu, můžete vybrat v cloudu. Cloud, který zvolíte je zcela v rámci vlastní ovládací prvek a je založený na požadavky na ochranu osobních údajů a suverenity vaše data.
 
 - RTO: Výpadek měřeno v hodinách
 - Plánovaný bod obnovení: Proměnné datové ztráty (v závislosti na četnost zálohování)
 - Topologie nasazení: aktivní/pasivní

#### <a name="planning-your-backup-strategy"></a>Plánování strategie zálohování

Plánování strategie zálohování a definování požadavků škálování začíná kvantifikace se počet instancí virtuálních počítačů, které musí být chráněny. Běžné strategie zálohování všechny virtuální počítače na všechny servery v prostředí je. S Azure zásobníku, existují však některé virtuální počítače, které mají být zálohovány. Virtuální počítače ve škálovací sadě například jsou považovány za dočasných prostředků, které může pocházet a přejděte někdy bez předchozího upozornění. Žádná trvanlivý data, která je potřeba chránit je uložené v samostatné úložiště, jako je například úložiště databáze nebo objekt.

Důležité informace pro zálohování virtuálních počítačů v Azure zásobníku:

 - **Kategorizaci**
    - Vezměte v úvahu modelu, kde uživatelé vyjádřit výslovný souhlas pro zálohování virtuálních počítačů.
    - Definujte obnovení smlouvu o úrovni služeb (SLA) na základě priority aplikace nebo dopad na firmu.
 - **Škálování**
    - Při Startovní velký počet nové virtuální počítače (Pokud je nutné zálohování), zvažte postupný zálohy.
    - Vyhodnoťte zálohování produkty, které můžete efektivně sběr a přenos zálohovaná data chcete-li minimalizovat obsah prostředku v řešení.
    - Vyhodnoťte zálohování produkty, které efektivně ukládat zálohovaná data pomocí přírůstkové nebo rozdílové zálohy. Chcete-li minimalizovat nutnost úplných záloh mezi všechny virtuální počítače v prostředí.
 - **Obnovení**
    - Zálohování produktů můžete obnovit virtuální disky, data aplikací v rámci stávajícího virtuálního počítače, nebo celý prostředků virtuálního počítače a přidruženými virtuálními disky. Schéma obnovení, které potřebujete, závisí na tom, jak máte v plánu obnovení aplikace a ovlivní vaše aplikace dobu obnovení. Například může být snazší k nasazení systému SQL server ze šablony a potom obnovte databáze místo obnovení celý virtuální počítač nebo sadu virtuálních počítačů.

### <a name="replicationmanual-failover"></a>Replikace nebo ruční převzetí služeb při selhání

Alternativní způsob podpora vysoké dostupnosti je replikace virtuálních počítačů vaší aplikace do jiného cloudu a spoléhají na ruční převzetí služeb při selhání. Replikace operačního systému, binární soubory aplikace a data aplikací můžete provést na úrovni virtuálního počítače nebo na úrovni operačního systému hosta. Převzetí služeb při selhání je spravovat pomocí další software, který není součástí aplikace.

S tímto přístupem je aplikace nasazená v jedné cloudu a jeho virtuální počítač se replikují do jiných cloudu. Pokud se aktivuje převzetí služeb při selhání, sekundární virtuální počítače nutné zapnout v druhé cloudu. V některých scénářích převzetí služeb při selhání vytvoří disky virtuálních počítačů a připojí k nim. Tento proces může trvat dlouhou dobu pro dokončení, zejména s víceúrovňových aplikací, který vyžaduje konkrétní spuštění pořadí. Také může být kroky, které se musí spustit před aplikace můžete začít požadavky obsluhy.

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

Takové disku, napájení, síťový port nebo uzel na úrovni Azure zásobníku fyzické redundance a infrastruktury služby dostupnosti chránit pouze na hardwaru chyb nebo selhání. Ale pokud vaše aplikace musí být k dispozici a nikdy ke ztrátě dat., musíte implementovat odolnost proti chybám nativně v aplikaci nebo používat další software umožňující odolnost proti chybám.

Nejdřív je potřeba zajistit, že aplikace, které virtuální počítače jsou nasazeny pomocí škálování nastaví na ochranu proti selhání uzlu úrovni. K ochraně proti cloudu přechod do stavu offline, stejnou aplikaci musí již být nasazeny na jiný Cloud, tak může pokračovat zpracování požadavků bez přerušení. Tento model se obvykle označuje na aktivní aktivní nasazení.

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

Tento článek poskytuje obecné pokyny pro ochranu uživatele virtuálních počítačích nasazených v zásobníku Azure. Informace o používání služby Azure k ochraně virtuální počítače uživatele najdete v části:

 - [Zálohování souborů a aplikací v zásobníku Azure pomocí Azure Backup](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Azure Backup Server podpora pro Azure zásobníku](https://docs.microsoft.com/azure/backup/ ) 
 - [Azure Site Recovery podpora pro Azure zásobníku](https://docs.microsoft.com/azure/site-recovery/)  

Další informace o partnera produkty, které nabízejí ochranu virtuálních počítačů v zásobníku Azure, najdete v tématu "[chrání aplikace a data v Azure zásobníku](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/)."
