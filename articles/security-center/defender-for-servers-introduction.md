---
title: Azure Defender pro servery – výhody a funkce
description: Přečtěte si o výhodách a funkcích Azure Defenderu pro servery.
author: memildin
ms.author: memildin
ms.date: 9/23/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 711963a60d5c75031ff676a9c7f1db47f20fe895
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275241"
---
# <a name="introduction-to-azure-defender-for-servers"></a>Seznámení s Azure Defenderem pro servery

Azure Defender pro servery přidává pro počítače se systémem Windows a Linux detekci hrozeb a pokročilou ochranu.

V případě Windows se Azure Defender integruje se službami Azure za účelem monitorování a ochrany počítačů se systémem Windows. Security Center prezentuje výstrahy a návrhy oprav ze všech těchto služeb ve snadno použitelném formátu.

Pro Linux shromažďuje Azure Defender záznamy auditu z počítačů se systémem Linux pomocí **auditu**, což je jeden z nejběžnějších rozhraní pro auditování Linux. auditované života v jádru hlavní. 


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>Jaké jsou výhody Azure Defenderu pro servery?

Mezi možnosti detekce a ochrany před hrozbami, které poskytuje Azure Defender pro servery, patří:

- **Integrovaná licence pro Microsoft Defender pro koncové body (jenom Windows)** – Azure Defender pro servery zahrnuje  [Microsoft Defender pro koncový bod](https://www.microsoft.com/microsoft-365/security/endpoint-defender). Společně poskytují komplexní možnosti detekce a odezvy koncových bodů (EDR). [Další informace](security-center-wdatp.md).

    Když Defender pro koncový bod zjistí hrozbu, aktivuje výstrahu. Výstraha je zobrazena v Security Center. Z Security Center můžete také provést přesun do programu Defender pro konzolu koncového bodu a provést podrobné šetření, které odhalí rozsah útoku. Přečtěte si další informace o programu Microsoft Defender pro koncový bod.

    > [!IMPORTANT]
    > Program **Microsoft Defender pro koncové body** je automaticky povolen na serverech se systémem Windows, které používají Security Center.

- **Posouzení ohrožení zabezpečení pro virtuální počítače** – skener ohrožení zabezpečení, který je součástí Azure Security Center, používá Qualys. 

    Qualys je jedním z špičkových nástrojů pro identifikaci ohrožení zabezpečení v reálném čase v Azure Virtual Machines. Nepotřebujete licenci Qualys ani účet Qualys – všechno se bez problémů zpracovává v Security Center. [Další informace](deploy-vulnerability-assessment-vm.md).

- **Přístup k virtuálnímu počítači JIT (just-in-time** ) – aktéri hrozeb aktivně prostupují přístupné počítače s otevřenými porty pro správu, jako je RDP nebo SSH. Všechny vaše virtuální počítače jsou potenciálními cíli pro útok. Když je virtuální počítač úspěšně napadený, používá se jako vstupní bod k útoku dalších prostředků ve vašem prostředí.

    Když povolíte Azure Defender pro servery, můžete použít přístup k virtuálnímu počítači za běhu k uzamknutí příchozího provozu do virtuálních počítačů. tím se sníží riziko útoků na útoky a v případě potřeby získáte snadný přístup k virtuálním počítačům. [Další informace](just-in-time-explained.md).

- **Monitorování integrity souborů (FIM)** – monitoring integrity souborů (FIM), označovaný také jako sledování změn, prověřuje soubory a Registry operačního systému, aplikačního softwaru a dalších pro změny, které by mohly naznačovat útok. Metoda porovnání se používá k určení, zda je aktuální stav souboru odlišný od poslední kontroly souboru. Toto porovnání můžete využít k určení, zda byly provedeny platné nebo podezřelé úpravy souborů.

    Když povolíte Azure Defender pro servery, můžete k ověření integrity souborů Windows, registrů Windows a souborů Linux použít službu FIM. [Další informace](security-center-file-integrity-monitoring.md).

- **Adaptivní řízení aplikací (AAC)** – Adaptivní řízení aplikací představují inteligentní a automatizované řešení pro definování seznamů povolených aplikací, které jsou pro vaše počítače bezpečné.

    Pokud jste povolili a nakonfigurovali Adaptivní řízení aplikací, obdržíte výstrahy zabezpečení, pokud nějaká aplikace běží jinak než ty, které jste definovali jako bezpečné. [Další informace](security-center-adaptive-application.md).

- **Adaptivní posílení zabezpečení sítě (Anh)** – použití skupin zabezpečení sítě (NSG) k filtrování provozu do a z prostředků, vylepšuje zabezpečení vaší sítě stav. Přesto však může být v některých případech, kdy je skutečný přenos toků přes NSG podmnožinou definovaných pravidel NSG. V těchto případech je možné zvýšit stav zabezpečení tím, že posílíte pravidla NSG, a to na základě skutečných schémat přenosů.

    Adaptivní posílení zabezpečení sítě poskytuje doporučení k dalšímu posílení pravidel NSG. Používá algoritmus strojového učení, který má vliv na skutečný provoz, známou důvěryhodnou konfiguraci, analýzu hrozeb a další indikátory ohrožení a pak poskytuje doporučení k tomu, aby se provozoval jenom z konkrétních řazených kolekcí IP/portů. [Další informace](security-center-adaptive-network-hardening.md).

- **Posílení zabezpečení hostitele Docker** – Azure Security Center identifikuje nespravované kontejnery hostované na virtuálních počítačích s IaaS Linux nebo jiné počítače se systémem Linux s kontejnery Docker. Security Center nepřetržitě vyhodnocuje konfiguraci těchto kontejnerů. Pak je porovná s centrem testování v Docker pro Internet Security (CIS). Security Center zahrnuje celou RuleSet srovnávacího testu služby CI Docker a upozorní vás, pokud vaše kontejnery nevyhovují žádnému z ovládacích prvků. [Další informace](harden-docker-hosts.md).

- **Detekce útoků typu soubor (jenom Windows)** – útoky pomocí souborů nemůžou do paměti vložit škodlivá data, aby se zabránilo detekci pomocí technik skenování na disku. Datová část útočníka pak přetrvává v paměti napadených procesů a provádí široké spektrum škodlivých aktivit.

  Díky detekci útoků bez souborů můžou automatizované techniky forenzní paměti identifikovat sady nástrojů, techniky a chování pro útoky bez souborů. Toto řešení pravidelně kontroluje počítač za běhu a extrahuje přehledy přímo z paměti procesů. Konkrétní přehledy zahrnují identifikaci: 

  - Dobře známé sady nástrojů a software pro dolování kryptografických softwaru 

  - Skrytý spustitelný kód, což je malý kus kódu, který se obvykle používá jako datová část při využívání ohrožení zabezpečení softwaru.

  - Vložený škodlivý spustitelný soubor v paměti procesu

  Detekce útoků bez souborů generuje podrobné výstrahy zabezpečení obsahující popisy s dalšími metadaty procesu, například síťovou aktivitou. Tím se zrychluje jejich třídění, korelace a doba odezvy za příjem dat. Tento přístup doplňuje řešení EDR založené na událostech a poskytuje zvýšené pokrytí detekce.

  Podrobnosti o výstrahách detekce útoků bez souborů najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-windows).

- **Auditované výstrahy systému Linux a integrace agenta Log Analytics (pouze Linux)** – auditované systémy se skládají z podsystému na úrovni jádra, který zodpovídá za monitorování volání systému. Filtruje je podle zadané sady pravidel a zapisuje zprávy pro ně do soketu. Security Center integruje funkce z auditovaného balíčku v rámci agenta Log Analytics. Tato integrace umožňuje shromažďování auditovaných událostí ve všech podporovaných distribucích systému Linux bez jakýchkoli požadavků.

    auditované záznamy jsou shromažďovány, obohaceny a agregovány do událostí pomocí agenta Log Analytics pro Linux. Security Center průběžně přidává nové analýzy, které používají signály Linux k detekci škodlivého chování na cloudových a místních počítačích se systémem Linux. Podobně jako funkce Windows tyto analýzy přesahují podezřelé procesy, pokusy o přihlášení k podezřelých, načítání modulů jádra a další činnosti. Tyto aktivity mohou znamenat, že počítač je buď napadený, nebo byl napadený.  

    Seznam výstrah pro Linux najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-linux).


## <a name="simulating-alerts"></a>Simulace výstrah

Výstrahy můžete simulovat stažením některého z následujících playbooky:

- Pro Windows: [Azure Security Center PlayBook: výstrahy zabezpečení](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf)

- Pro Linux: [Azure Security Center PlayBook: Linux – detekce](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf).




## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o Azure Defenderu pro servery. 

Související materiály najdete v následujících článcích: 

- Bez ohledu na to, jestli je výstraha vygenerovaná Security Center nebo přijatá Security Center z jiného bezpečnostního produktu, můžete ji exportovat. Pokud chcete exportovat výstrahy do Azure Sentinel, všech SIEM třetích stran nebo jakýchkoli jiných externích nástrojů, postupujte podle pokynů v tématu [Export výstrah do Siem](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Povolit Azure Defender](security-center-pricing.md)