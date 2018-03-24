---
title: Replikace dat v Azure Storage | Microsoft Docs
description: Data ve vašem účtu úložiště Microsoft Azure se replikují pro odolnost a vysokou dostupnost. Možnosti replikace zahrnují místně redundantní úložiště (LRS), zónově redundantní úložiště (ZRS), geograficky redundantní úložiště (GRS) a geograficky redundantní úložiště s přístupem pro čtení (RA-GRS).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: 600b66af3b7da24c5a40d09d5cdf76f2d5be67ac
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-storage-replication"></a>Účet replikace Azure Storage

Data na vašem účtu Microsoft Azure Storage se vždy replikují, aby byla zajištěna jejich stálost a vysoká dostupnost. Replikace zkopíruje data, takže je chráněn proti selhání hardwaru, zachování vaší aplikace doby provozu. 

Je možné replikovat data ve stejném datovém centru, napříč datovými centry ve stejné oblasti, nebo v oblastech. Pokud vaše data se replikují v několika datových centrech nebo v oblastech, jsou taky chráněná závažné selhání v jednom umístění.

Replikace zajišťuje, že váš účet úložiště splňuje [smlouvu o úrovni služeb (SLA) pro Storage](https://azure.microsoft.com/support/legal/sla/storage/) i při selhání. Podívejte se do smlouvy SLA na informace o zárukách služby Azure Storage na stálost a dostupnost.

Při vytvoření účtu úložiště si můžete vybrat jednu z těchto možností replikace:

* [Místně redundantní úložiště (LRS)](#locally-redundant-storage)
* [Zónově redundantní úložiště (ZRS)](#zone-redundant-storage)
* [Geograficky redundantní úložiště (GRS)](#geo-redundant-storage)
* [Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)](#read-access-geo-redundant-storage)

Místně redundantní úložiště (LRS) je výchozí možností při vytváření účtu úložiště.

Následující tabulka poskytuje rychlý přehled o rozdílech mezi LRS, ZRS, GRS a RA-GRS. Následující části tohoto článku adres každý typ replikace podrobněji.

| Strategie replikace | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Data se replikují přes několik datových center. |Ne |Ano |Ano |Ano |
| Ze sekundární lokality, stejně jako primární umístění můžete číst data. |Ne |Ne |Ne |Ano |
| Určená k poskytnutí ___ odolnost objektů během daného roku. |alespoň % 99.999999999 (11 společnosti 9)|alespoň % 99.9999999999 (12 na 9)|alespoň % 99.99999999999999 (16 na 9)|alespoň % 99.99999999999999 (16 na 9)|

V tématu [Azure Storage – ceny](https://azure.microsoft.com/pricing/details/storage/) pro informace o cenách pro možnosti různých redundance.

> [!NOTE]
> Premium Storage podporuje pouze místně redundantní úložiště (LRS). Informace o Premium Storage najdete v tématu [úložiště Premium: vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](../../virtual-machines/windows/premium-storage.md).
>

## <a name="locally-redundant-storage"></a>(Locally redundant storage) Místně redundantní úložiště
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

## <a name="zone-redundant-storage"></a>Redundantní úložiště zóny
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="zrs-classic-accounts"></a>ZRS klasické účty

Existující funkce ZRS se nyní označuje jako ZRS Classic. Účty ZRS Classic jsou k dispozici jen pro objekty blob bloku v účtech úložiště pro obecné účely verze 1. 

ZRS Classic replikuje data asynchronně mezi datovými centry v jedné až dvou oblastech. Replika nemusí být k dispozici, pokud společnost Microsoft nezahájí převzetí služeb při selhání a přechod na sekundární data. 

ZRS klasické účty nelze převést na nebo z LRS, GRS nebo RA-GRS. ZRS klasické účty také nepodporují metriky nebo protokolování.   

Jakmile ZRS je všeobecně dostupná v oblasti, už nebudete moct vytvořit účet ZRS Classic z portálu v daném regionu, ale můžete vytvořit jeden jinými způsoby.  
Proces automatické migrace z klasického ZRS ZRS bude poskytnuta v budoucnu.

Můžete ručně migrovat data účtu ZRS do nebo z účtu LRS, ZRS Classic, GRS a RAGRS. Můžete provést tento ruční migrace pomocí nástroje AzCopy, Azure Storage Explorer, prostředí Azure PowerShell, rozhraní příkazového řádku Azure nebo jeden z knihovny klienta Azure Storage.

> [!NOTE]
> ZRS klasické účty jsou plánované pro vyřazení a požadované migrace na 31. března 2021. Společnost Microsoft bude posílat podrobnosti ZRS Classic zákazníkům před vyřazení.

Další otázky jsou řešeny v [nejčastější dotazy](#frequently-asked-questions) části. 

## <a name="geo-redundant-storage"></a>Geograficky redundantní úložiště
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="read-access-geo-redundant-storage"></a>Geograficky redundantní úložiště s přístupem pro čtení
Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) maximalizuje dostupnost pro váš účet úložiště. RA-GRS poskytuje přístup jen pro čtení k datům v sekundárním umístění, kromě geografická replikace v rámci dvou oblastí.

Když povolíte přístup jen pro čtení k datům v sekundární oblasti, vaše data jsou k dispozici na sekundárním koncovým bodem, a také na primární koncový bod pro váš účet úložiště. Sekundární koncový bod je podobná primární koncový bod, ale připojí přípona `–secondary` k názvu účtu. Například, pokud je váš primární koncový bod služby objektů Blob `myaccount.blob.core.windows.net`, pak je sekundární koncový bod `myaccount-secondary.blob.core.windows.net`. Přístupové klíče účtu úložiště jsou stejné pro obě primární a sekundární koncové body.

Některé aspekty při používání RA-GRS mějte na paměti:

* Aplikace má ke správě kterému koncovému bodu je interakci s při použití RA-GRS.
* Vzhledem k tomu, že asynchronní replikaci zahrnuje zpoždění, změny, které ještě nebyla replikována do sekundární oblasti mohou být ztraceny, pokud data nelze obnovit z primární oblasti, například v případě místní havárie.
* Pokud Microsoft zahájí převzetí služeb při selhání pro sekundární oblast, budete mít ke čtení a zápis do dat po převzetí služeb po dokončení. Další informace najdete v tématu [pokyny pro zotavení po havárii](../storage-disaster-recovery-guidance.md).
* RA-GRS, je určena pro účely vysokou dostupnost. Škálovatelnost pokyny najdete v článku [kontrolní seznam výkonu](../storage-performance-checklist.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

<a id="howtochange"></a>
#### <a name="1-how-can-i-change-the-geo-replication-type-of-my-storage-account"></a>1. Jak můžete změnit typ geografická replikace svůj účet úložiště?

   Geografická replikace typ účtu úložiště můžete změnit pomocí [portál Azure](https://portal.azure.com/), [prostředí Azure Powershell](storage-powershell-guide-full.md), nebo jeden z knihovny klienta Azure Storage.

   > [!NOTE]
   > ZRS účty nemůže být převedená LRS nebo GRS. Podobně existující účet LRS nebo GRS nelze převést na účet ZRS.
    
<a id="changedowntime"></a>
#### <a name="2-does-changing-the-replication-type-of-my-storage-account-result-in-down-time"></a>2. Podporuje Změna typu replikace výsledku Moje úložiště účet v výpadek?

   Ne, změna typu replikace účtu úložiště nevede výpadek.

<a id="changecost"></a>
#### <a name="3-are-there-additional-costs-to-changing-the-replication-type-of-my-storage-account"></a>3. Existují další poplatky za změnu typ replikace pro svůj účet úložiště?

   Ano. Pokud změníte na GRS (nebo RA-GRS) pro účet úložiště LRS, platit dalších poplatků odchozí účastnící se kopírování existujících dat z primárního umístění do sekundárního umístění. Po počáteční data se zkopíruje, neexistují žádné další další odchozí poplatky za geografickou replikaci z primárního na sekundární umístění. Podrobnosti o šířku pásma poplatky, najdete v části [Azure Storage – ceny stránky](https://azure.microsoft.com/pricing/details/storage/blobs/).

   Pokud změníte na LRS GRS, je bez dalších nákladů, ale data se odstraní ze sekundárního umístění.

<a id="ragrsbenefits"></a>
#### <a name="4-how-can-ra-grs-help-me"></a>4. Jak RA-GRS mi může pomoci?

   GRS úložiště poskytuje replikaci vašich dat z primárního na sekundární oblasti, která je stovky miles od primární oblasti. S GRS vaše data byla odolná i v případě výpadku dokončení místní nebo havárii, ve kterém není použitelná pro obnovení primární oblasti. RA-GRS úložiště nabízí replikace GRS a přidává možnost číst data ze sekundárního umístění. Doporučení týkající se návrhu pro zajištění vysoké dostupnosti, najdete v části [navrhování vysoce dostupné aplikace pomocí úložiště RA-GRS](../storage-designing-ha-apps-with-ragrs.md).

<a id="lastsynctime"></a>
#### <a name="5-is-there-a-way-to-figure-out-how-long-it-takes-to-replicate-my-data-from-the-primary-to-the-secondary-region"></a>5. Existuje způsob, jak zjistit, jak dlouho trvá replikaci Moje dat z primárního na sekundární oblast?

   Pokud používáte RA-GRS úložiště, můžete zkontrolovat, čas poslední synchronizace účtu úložiště. Čas poslední synchronizace je hodnota, datum a čas GMT. Všechny primární zápisy před čas poslední synchronizace byla úspěšně zapsána do sekundárního umístění, což znamená, že jsou k dispozici čtení ze sekundárního umístění. Primární zapíše po čas poslední synchronizace může nebo nemusí být k dispozici pro čtení ještě. Můžete dotazovat pomocí této hodnoty [portál Azure](https://portal.azure.com/), [prostředí Azure PowerShell](storage-powershell-guide-full.md), nebo z jednoho z knihovny klienta Azure Storage.

<a id="outage"></a>
#### <a name="6-if-there-is-an-outage-in-the-primary-region-how-do-i-switch-to-the-secondary-region"></a>6. Pokud dojde k výpadku v primární oblasti, jak lze přepnout do sekundární oblasti?

   Další informace najdete v tématu [co dělat, když dojde k výpadku Azure Storage](../storage-disaster-recovery-guidance.md).

<a id="rpo-rto"></a>
#### <a name="7-what-is-the-rpo-and-rto-with-grs"></a>7. Co je plánovaný bod obnovení a RTO s GRS?

   **Cíl bodu obnovení (RPO):** v GRS a RA-GRS, úložiště služby asynchronně geo replikují data z primární na sekundární umístění. V případě významnější havárie regionální v primární oblasti provede Microsoft převzetí služeb při selhání pro sekundární oblast. Pokud dojde převzetí služeb při selhání, nedávné změny, které dosud nebyly geograficky replikované mohou být ztraceny. Počet minut potenciální – ztráta dat se označuje jako plánovaný bod obnovení a znamená bod v čase, do kterých můžete obnovit data. Úložiště Azure obvykle má RPO méně než 15 minut, i když na jak dlouho se geografická replikace je aktuálně žádné SLA trvá.

   **Cíli času obnovení (RTO):** RTO se rozumí míra doba potřebná k provedení převzetí a získat zpět do režimu online účet úložiště. Čas k provedení převzetí zahrnuje následující akce:

   * Čas Microsoft vyžaduje k určení, zda lze obnovit data v primární lokalitě nebo pokud převzetí služeb při selhání je nutné.
   * Čas k provedení převzetí služeb při selhání účtu úložiště tak, že změníte primární záznamy DNS tak, aby odkazovaly do sekundárního umístění.

   Společnost Microsoft má je zodpovědností vážně zachování vaše data. Pokud je pravděpodobné, že obnovení dat v primární oblasti, bude společnost Microsoft zpoždění převzetí služeb při selhání a zaměřit se na obnovení dat. Budoucí verze služby vám umožní spustit převzetí služeb při selhání na úrovni účtu, abyste mohli řídit RTO, sami.

#### <a name="8-what-azure-storage-objects-does-zrs-support"></a>8. Které objekty Azure Storage podporuje ZRS? 
Objekty BLOB bloku, objekty BLOB stránky (s výjimkou těchto disků zálohování virtuálních počítačů), tabulky, soubory a fronty. 

#### <a name="9-does-zrs-also-include-geo-replication"></a>9. ZRS také zahrnuje geografická replikace? 
ZRS aktuálně geografická replikace nepodporuje. Pokud váš scénář vyžaduje replikace mezi oblastmi za účelem zotavení po havárii, použijte účet úložiště GRS nebo RA-GRS.   

#### <a name="10-what-happens-when-one-or-more-zrs-zones-go-down"></a>10. Co se stane, když jeden nebo více zón ZRS přejděte? 
Při první zónu přestane fungovat, i nadále ZRS zápisu repliky data mezi dvěma zbývající zón v oblasti. Pokud zónu druhý přestane fungovat, bude oprávnění ke čtení a zápisu nedostupné, dokud alespoň dvě zóny jsou k dispozici znovu. 

## <a name="next-steps"></a>Další postup
* [Návrh aplikace s vysokou dostupností pomocí RA-GRS úložiště](../storage-designing-ha-apps-with-ragrs.md)
* [Ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Informace o účtech úložiště Azure](../storage-create-storage-account.md)
* [Azure Storage škálovatelnosti a cílech výkonnosti](storage-scalability-targets.md)
* [Microsoft Azure Storage redundance možnosti a přístup pro čtení geograficky redundantní úložiště ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [STUDIE Sosp - Azure Storage: Vysoce dostupný cloudové úložiště služby se silnou konzistenci](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
