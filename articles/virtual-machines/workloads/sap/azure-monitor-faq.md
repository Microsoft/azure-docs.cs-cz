---
title: Nejčastější dotazy – Azure Monitor pro řešení SAP | Microsoft Docs
description: V tomto článku se dozvíte odpovědi na nejčastější dotazy týkající se Azure Monitor řešení SAP.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 3732189c1d2e09b648a2fba0a39e7e4113a76d48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675952"
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
V tuto chvíli můžete monitorovat databázi HANA, podkladovou infrastrukturu, cluster s vysokou dostupností a Microsoft SQL Server ve verzi Public Preview.  

 - **Nahrazuje tato služba službu SAP Solution Manager?**  
No. Zákazníci si pořád můžou používat SAP Solution Manager pro obchodní procesy monitoring.  

 - **Jaká je hodnota této služby v rámci tradičních řešení, jako je SAP HANA řídicí panel/Studio?**  
Azure Monitor pro řešení SAP není specifická pro databázi HANA. Azure Monitor pro řešení SAP podporuje také AnyDB.  

## <a name="next-steps"></a>Další kroky

- Vytvořte první Azure Monitor pro prostředek řešení SAP.
