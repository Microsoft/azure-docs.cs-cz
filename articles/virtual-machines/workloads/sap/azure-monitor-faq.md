---
title: Nejčastější dotazy – Azure Monitor pro řešení SAP | Microsoft Docs
description: V tomto článku se dozvíte odpovědi na nejčastější dotazy týkající se Azure Monitor řešení SAP.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 4b84f07f637b0a8925dec96c8c609101247ffd64
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377120"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>Nejčastější dotazy k Azure Monitor pro řešení SAP (Preview)
## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure Monitor řešení SAP.  

 - **Musím platit za Azure Monitor pro řešení SAP?**  
Pro Azure Monitor pro řešení SAP se neúčtují žádné licenční poplatky.  
Zákazníkům se ale zodpovídá za to, že nesou náklady na komponenty spravovaných skupin prostředků.  

 - **Ve kterých oblastech je tato služba dostupná pro veřejnou verzi Preview?**  
Ve verzi Public Preview bude tato služba k dispozici v Východní USA 2, Západní USA 2, Východní USA a Západní Evropa.  

 - **Potřebuji mít oprávnění k povolení nasazení spravované skupiny prostředků v předplatném?**  
Ne, explicitní oprávnění se nevyžadují.  

 - **Kde se nachází virtuální počítač kolektoru?**  
V okamžiku nasazení Azure Monitor pro prostředek řešení SAP doporučujeme, aby si zákazníci zvolili stejnou virtuální síť pro sledování prostředků jako jejich Server SAP HANA. Proto se doporučuje, aby se virtuální počítač kolektoru nacházel ve stejné virtuální síti jako Server SAP HANA. Pokud zákazníci používají jinou databázi než HANA, bude se virtuální počítač kolektoru nacházet ve stejné virtuální síti jako databáze mimo HANA.  

 - **Které verze HANA se podporují?**  
HANA 1,0 SPS 12 (rev. 120 nebo novější) a HANA 2,0 SPS03 nebo vyšší  

 - **Které konfigurace nasazení HANA se podporují?**  
Podporují se tyto konfigurace:
   - Jeden uzel (horizontální navýšení kapacity) a vícenásobný uzel (škálování na více instancí)  
   - Kontejner jedné databáze (HANA 1,0 SPS 12) a více databázových kontejnerů (HANA 1,0 SPS 12 nebo HANA 2,0)  
   - Automatické převzetí služeb při selhání hostitele (n + 1) a HSR  

 - **Které verze SQL Server podporovány?**  
SQL Server 2012 SP4 nebo vyšší.  

 - **Které konfigurace SQL Server podporovány?**  
Podporují se tyto konfigurace:
   - Výchozí nebo pojmenované samostatné instance ve virtuálním počítači  
   - Clusterované instance nebo instance v konfiguraci AlwaysOn, pokud buď používají virtuální název clusterovaného prostředku nebo název naslouchacího procesu AlwaysOn. V současné době se neshromažďují žádné metriky konkrétního clusteru ani AlwaysOn.    
   - Azure SQL Database (PAAS) se momentálně nepodporuje.  

 - **Co se stane, když omylem odstraníte spravovanou skupinu prostředků?**  
Spravovaná skupina prostředků je ve výchozím nastavení uzamčena. Z toho důvodu je pravděpodobné nechtěného odstranění spravované skupiny prostředků zákazníky minuscule.  
Pokud zákazník odstraní spravovanou skupinu prostředků, Azure Monitor pro řešení SAP přestane fungovat. Zákazník bude muset nasadit nové Azure Monitor pro prostředky řešení SAP a začít znovu.  

 - **Jaké role v předplatném Azure potřebuji k nasazení Azure Monitor pro prostředek řešení SAP?**  
Role přispěvatele.  

 - **Jaká je smlouva SLA k tomuto produktu?**  
Verze Preview jsou vyloučené ze smluv o úrovni služeb. Přečtěte si celý licenční termín prostřednictvím Azure Monitor pro Image řešení SAP na webu Marketplace.  

 - **Můžu přes toto řešení monitorovat celý na šířku?**  
V tuto chvíli můžete monitorovat databázi HANA, podkladovou infrastrukturu, cluster s vysokou dostupností, Microsoft SQL Server, SAP NetWeaver dostupnost a metriky dostupnosti instance aplikace SAP ve verzi Public Preview.  

 - **Nahrazuje tato služba službu SAP Solution Manager?**  
No. Zákazníci si pořád můžou používat SAP Solution Manager pro obchodní procesy monitoring.  

 - **Jaká je hodnota této služby v rámci tradičních řešení, jako je SAP HANA řídicí panel/Studio?**  
Azure Monitor pro řešení SAP není specifická pro databázi HANA. Azure Monitor pro řešení SAP podporuje také AnyDB.  

- **Které verze SAP NetWeaver jsou podporovány?**  
SAP NetWeaver 7,0 nebo vyšší.  

- **Které konfigurace SAP NetWeaver se podporují?**  
Podporuje konfigurace aplikačního serveru SAP NetWeaver pro ABAP, Java a duální Stack.

- **Proč je potřeba zrušit ochranu metod pro monitorování aplikací SAP NetWeaver?**  
V SAP releases >= 7,3, většina metod WebService je ve výchozím nastavení chráněná. Aby bylo možné načíst metriky dostupnosti a výkonu voláním těchto metod, je třeba zrušit ochranu následujících metod: GetQueueStatistic, ABAPGetWPTable, GetProcessList, EnqGetStatistic a GetSystemInstancelist.

- **Je nějaké riziko, že se nechrání WebMethod SAPCONTROL?**  
Obecně [platí, že](https://launchpad.support.sap.com/#/notes/1439348)Pokud si zákazníci chtějí omezit nebo zakázat přístup k nechráněným webmethodům prostřednictvím portů serveru (5XX13/5XX14) sapstartsrv, nezabezpečují SAPCONTROL webMethods bezpečnostní riziko. to uděláte tak, že přidáte filtr do seznamu SAP Access Control List (ACL). [Poznámka OSS](https://service.sap.com/sap/support/notes/1495075) popisuje požadovanou konfiguraci k dosažení. 

- **Potřebuji restartovat instance SAP po provedení konfigurace systému pro nastavení poskytovatele SAP NetWeaver?**  
Ano, jakmile budete mít k dispozici nechráněné metody přes změny konfigurace SAP, bude nutné restartovat příslušné systémy SAP, aby bylo zajištěno, že se změny konfigurace aktualizují.  

## <a name="next-steps"></a>Další kroky

- Vytvořte první Azure Monitor pro prostředek řešení SAP.
