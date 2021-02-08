---
title: Adaptivní řízení aplikací v Azure Security Center
description: Tento dokument vám pomůže použít adaptivní řízení aplikací v Azure Security Center k povolení seznamu aplikací, které běží na počítačích Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/07/2021
ms.author: memildin
ms.openlocfilehash: cc35f9ee25511faec5723b49763cbdae8963e88b
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820751"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>Použití adaptivních řízení aplikací k omezení ploch útoků počítačů

Přečtěte si o výhodách Azure Security Center adaptivních řízení aplikací a o tom, jak můžete zvýšit zabezpečení pomocí této inteligentní funkce řízené daty.


## <a name="what-are-security-centers-adaptive-application-controls"></a>Co jsou Security Center Adaptivní řízení aplikací?

Adaptivní řízení aplikací je inteligentní a automatizované řešení pro definování seznamů povolených aplikací, které jsou pro vaše počítače bezpečné. 

Organizace často používají kolekce počítačů, které rutinně spouštějí stejné procesy. Security Center pomocí strojového učení analyzuje aplikace běžící na vašich počítačích a vytvoří seznam známých bezpečných softwaru. Seznamy povolených aplikací jsou založené na konkrétních úlohách Azure a můžete je dále přizpůsobit pomocí následujících pokynů.

Pokud jste povolili a nakonfigurovali Adaptivní řízení aplikací, obdržíte výstrahy zabezpečení, pokud nějaká aplikace běží jinak než ty, které jste definovali jako bezpečné.


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>Jaké jsou výhody adaptivních řízení aplikací?

Definováním seznamů známých aplikací v bezpečí a generování výstrah v případě, že se spustí cokoli jiného, můžete dosáhnout více cílů posílení zabezpečení:

- Identifikace potenciálního malwaru, dokonce i těch, které by mohly být v antimalwarových řešeních vynechány
- Zlepšení dodržování předpisů pomocí místních zásad zabezpečení, které určují použití výhradně licencovaného softwaru
- Vyhnout se spouštění starých nebo nepodporovaných aplikací
- Zabránění konkrétnímu softwaru, který je zakázán vaší organizací
- Zvýšení zraku aplikací, které přistupují k citlivým datům

Nejsou k dispozici žádné možnosti vynucení a žádné nejsou plánovány. Adaptivní řízení aplikací je určeno k poskytování výstrah zabezpečení v případě, že některá aplikace běží jinak než ty, které jste definovali jako bezpečné.

## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Obecná dostupnost (GA)|
|Stanov|Vyžaduje [Azure Defender pro servery](defender-for-servers-introduction.md) .|
|Podporované počítače:|![](./media/icons/yes-icon.png)Počítače Azure a jiné než Azure se systémem Windows a Linux<br>![Počítače s ](./media/icons/yes-icon.png) [obloukem Azure ARC](../azure-arc/index.yml)|
|Požadované role a oprávnění:|Role **Čtenář zabezpečení** a **Čtenář** můžou zobrazovat skupiny i seznamy známých aplikací, které jsou v bezpečí.<br>Role **Přispěvatel** a **Správce zabezpečení** můžou upravovat skupiny i seznamy známých aplikací, které jsou v bezpečí.|
|Cloud|![Ano](./media/icons/yes-icon.png) Komerční cloudy<br>![Ano](./media/icons/yes-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>Povolit ovládací prvky aplikace pro skupinu počítačů

Pokud Security Center identifikoval skupiny počítačů ve vašich předplatných, které konzistentně spouštějí podobnou sadu aplikací, zobrazí se vám následující doporučení: **Adaptivní řízení aplikací pro definování bezpečných aplikací by mělo být na vašich počítačích povolené**.

Vyberte doporučení nebo otevřete stránku Adaptivní řízení aplikací, kde se zobrazí seznam navrhovaných aplikací a skupin počítačů, které jsou v bezpečí.

1. Otevřete řídicí panel Azure Defender a z oblasti Pokročilá ochrana vyberte **Adaptivní řízení aplikací**.

    :::image type="content" source="./media/security-center-adaptive-application/opening-adaptive-application-control.png" alt-text="Otevření adaptivních řízení aplikací z řídicího panelu Azure" lightbox="./media/security-center-adaptive-application/opening-adaptive-application-control.png":::

    Otevře se stránka **Adaptivní řízení aplikací** se skupinami virtuálních počítačů na následujících kartách:

    - **Nakonfigurováno** – skupiny počítačů, které již mají definovaný seznam povolených aplikací. Na každé skupině se zobrazí konfigurovaná karta:
        - počet počítačů ve skupině
        - Nedávné výstrahy

    - **Doporučené** – skupiny počítačů, které konzistentně spouštějí stejné aplikace a nemají nakonfigurovaný seznam povolených aplikací. Pro tyto skupiny doporučujeme povolit Adaptivní řízení aplikací.
    
      > [!TIP]
      > Pokud se zobrazí název skupiny s předponou "kontrolor", obsahuje počítače s částečně konzistentním seznamem aplikací. Security Center nemůže vidět vzor, ale doporučuje zkontrolovat tuto skupinu, abyste viděli, jestli můžete ručně definovat některá pravidla adaptivního řízení aplikací, jak _je_ popsáno v tématu [Úprava pravidla adaptivního řízení aplikací skupiny](#edit-a-groups-adaptive-application-controls-rule).
      >
      > Počítače z této skupiny můžete také přesunout do jiných skupin, jak je popsáno v tématu [Přesun počítače z jedné skupiny do druhé](#move-a-machine-from-one-group-to-another).

    - **Žádné doporučení** – počítače bez definovaného seznamu povolených aplikací, které tuto funkci nepodporují. Počítač může být na této kartě z následujících důvodů:
      - Chybí agent Log Analytics.
      - Agent Log Analytics neodesílá události.
      - Jedná se o počítač s Windows s již existujícími zásadami [AppLockeru](/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) , které jsou povolené buď pomocí objektu zásad skupiny, nebo místních zásad zabezpečení.

      > [!TIP]
      > Security Center potřebuje k definování jedinečných doporučení na skupinu počítačů aspoň dva týdny dat. Na kartě **bez doporučení** se zobrazí počítače, které byly nedávno vytvořeny nebo které patří k předplatným, která byla nedávno povolena pomocí Azure Defenderu.


1. Otevřete kartu **Doporučené** . Zobrazí se skupiny počítačů s doporučenými seznamy povolených.

   ![Doporučená karta](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. Vyberte skupinu. 

1. Pokud chcete nakonfigurovat nové pravidlo, přečtěte si různé části této stránky **Konfigurace pravidel řízení aplikací** a obsah, který bude pro tuto konkrétní skupinu počítačů jedinečný:

   ![Konfigurace nového pravidla](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **Vybrat počítače** – ve výchozím nastavení jsou vybrané všechny počítače v identifikované skupině. Pokud je chcete z tohoto pravidla odebrat, zrušte výběr.
   
   1. **Doporučené aplikace** – Projděte si seznam aplikací, které jsou společné pro počítače v této skupině, a doporučujeme, abyste je mohli spustit.
   
   1. **Další aplikace** – Projděte si tento seznam aplikací, které se na počítačích v této skupině vyskytují méně často, nebo se označují jako zneužitelné. Výstražná ikona označuje, že by útočník mohl použít určitou aplikaci k obejít seznamu povolených aplikací. Doporučujeme pečlivě zkontrolovat tyto aplikace.

      > [!TIP]
      > Oba seznamy aplikací zahrnují možnost omezit konkrétní aplikaci na určité uživatele. Pokud je to možné, přijímají princip nejnižších oprávnění.
      > 
      > Aplikace jsou definovány jejich vydavateli, pokud aplikace nemá informace o vydavateli (není podepsané), vytvoří se pravidlo cesty pro úplnou cestu konkrétní aplikace.

   1. Pokud chcete pravidlo použít, vyberte **audit**. 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>Upravit pravidlo adaptivních řízení aplikací pro skupinu

Můžete se rozhodnout upravit seznam povolených počítačů pro skupinu počítačů kvůli známým změnám ve vaší organizaci. 

Úprava pravidel pro skupinu počítačů:

1. Otevřete řídicí panel Azure Defender a z oblasti Pokročilá ochrana vyberte **Adaptivní řízení aplikací**.

1. Na kartě **konfigurované** vyberte skupinu s pravidlem, které chcete upravit.

1. Projděte si různé části stránky **Konfigurovat pravidla řízení aplikací** , jak je popsáno v tématu [Povolení adaptivních řízení aplikací u skupiny počítačů](#enable-application-controls-on-a-group-of-machines).

1. Volitelně můžete přidat jedno nebo více vlastních pravidel:

   1. Vyberte **Přidat pravidlo**.

      ![Přidat vlastní pravidlo](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. Pokud definujete známou bezpečnou cestu, změňte **Typ pravidla** na ' path ' a zadejte jednu cestu. V cestě můžete zadat zástupné znaky.
   
      > [!TIP]
      > Některé scénáře, které mohou být užitečné pro zástupné znaky v cestě:
      > 
      > * Použití zástupného znaku na konci cesty pro povolení všech spustitelných souborů v této složce a podsložkách.
      > * Použití zástupných znaků uprostřed cesty k povolení známého názvu spustitelného souboru s měněným názvem složky (například osobní složky uživatelů obsahující známý spustitelný soubor, automaticky generované názvy složek atd.).
  
   1. Definujte povolené uživatele a typy chráněných souborů.

   1. Po dokončení definování pravidla vyberte **Přidat**.

1. Chcete-li změny použít, vyberte možnost **Uložit**.


## <a name="review-and-edit-a-groups-settings"></a>Kontrola a úprava nastavení skupiny

1. Pokud chcete zobrazit podrobnosti a nastavení skupiny, vyberte **Nastavení skupiny** .

    V tomto podokně se zobrazuje název skupiny (kterou lze upravit), typ operačního systému, umístění a další relevantní podrobnosti.

    :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-group-settings.png" alt-text="Stránka nastavení skupiny pro Adaptivní řízení aplikací" lightbox="./media/security-center-adaptive-application/adaptive-application-group-settings.png":::

1. Volitelně můžete změnit název skupiny nebo režimy ochrany typu souboru.

1. Vyberte **použít** a **Uložit**.



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>Reagovat na pravidla povolených v zásadách adaptivního řízení aplikací, která by se měla aktualizovat: doporučení

Toto doporučení se zobrazí, když Security Center Machine Learning identifikuje potenciálně legitimní chování, které dříve nebylo povoleno. Doporučení navrhuje nová pravidla pro existující definice, aby se snížil počet falešně pozitivních výstrah.

Náprava problémů:

1. Na stránce doporučení vyberte **pravidla povolených v zásadách adaptivního řízení aplikací, která by měla být aktualizována** doporučením, aby se zobrazily skupiny s nově identifikovaným a potenciálně oprávněným chováním.

1. Vyberte skupinu s pravidlem, které chcete upravit.

1. Projděte si různé části stránky **Konfigurovat pravidla řízení aplikací** , jak je popsáno v tématu [Povolení adaptivních řízení aplikací u skupiny počítačů](#enable-application-controls-on-a-group-of-machines).

1. Chcete-li změny použít, vyberte **audit**.




## <a name="audit-alerts-and-violations"></a>Výstrahy a porušení auditu

1. Otevřete řídicí panel Azure Defender a z oblasti Pokročilá ochrana vyberte **Adaptivní řízení aplikací**.

1. Pokud chcete zobrazit skupiny s počítači s nedávnými výstrahami, Projděte si skupiny uvedené na stránce **konfigurovaná** karta.

1. Pokud chcete prozkoumat další postup, vyberte skupinu.

   ![Nedávné výstrahy](./media/security-center-adaptive-application/recent-alerts.png)

1. Pro další podrobnosti a seznam ovlivněných počítačů vyberte výstrahu.



## <a name="move-a-machine-from-one-group-to-another"></a>Přesunutí počítače z jedné skupiny do druhé

Když přesunete počítač z jedné skupiny do druhé, uplatní se u něj zásada řízení aplikací na nastavení skupiny, do které jste ji přesunuli. Počítač můžete také přesunout z nakonfigurované skupiny do nenakonfigurované skupiny. tím dojde k odebrání všech pravidel řízení aplikací, která byla použita pro daný počítač.

1. Otevřete řídicí panel Azure Defender a z oblasti Pokročilá ochrana vyberte **Adaptivní řízení aplikací**.

1. Na stránce **Adaptivní řízení aplikací** vyberte z karty **nakonfigurovaná** skupinu obsahující počítač, který chcete přesunout.

1. Otevřete seznam  **nakonfigurovaných počítačů**.

1. V nabídce počítače otevřete tři tečky na konci řádku a vyberte **přesunout**. Otevře se podokno **přesunout počítač do jiné skupiny** .

1. Vyberte cílovou skupinu a vyberte **přesunout počítač**.

1. Výběrem možnosti **Uložit** změny uložte.





## <a name="manage-application-controls-via-the-rest-api"></a>Správa ovládacích prvků aplikace pomocí REST API 

Pro správu adaptivních řízení aplikací programově použijte naši REST API. 

Úplná dokumentace k rozhraní API [najdete tady](/rest/api/securitycenter/adaptiveapplicationcontrols).

Některé funkce, které jsou dostupné z REST API:

* **Seznam** načte všechna doporučení skupiny a poskytuje JSON s objektem pro každou skupinu.

* **Získejte** kód JSON s úplnými daty doporučení (tj. seznam počítačů, pravidel vydavatelů a cest atd.).

* **Vložením** nakonfigurujete pravidlo (pro tento požadavek použijte kód JSON, který jste **získali** jako text).
 
   > [!IMPORTANT]
   > Funkce **Put** očekává méně parametrů, než JSON vrácený příkazem Get obsahuje.
   >
   > Než použijete JSON v žádosti PUT, odeberte následující vlastnosti: recommendationStatus, configurationStatus, problémy, Location a sourceSystem.


## <a name="faq---adaptive-application-controls"></a>Nejčastější dotazy – Adaptivní řízení aplikací

### <a name="are-there-any-options-to-enforce-the-application-controls"></a>Existují nějaké možnosti, jak vymáhat ovládací prvky aplikace?
Nejsou k dispozici žádné možnosti vynucení a žádné nejsou plánovány. Adaptivní řízení aplikací je určeno k poskytování **výstrah zabezpečení** v případě, že některá aplikace běží jinak než ty, které jste definovali jako bezpečné. Mají řadu výhod ([Jaké jsou výhody adaptivních řízení aplikací?](#what-are-the-benefits-of-adaptive-application-controls)) a jsou extrémně přizpůsobitelná, jak je znázorněno na této stránce.

 

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak použít adaptivní řízení aplikací v Azure Security Center k definování seznamů povolených aplikací běžících na počítačích Azure a mimo Azure. Další informace o některých dalších funkcích ochrany úloh v cloudu Security Center najdete v tématech:

* [Principy přístupu k virtuálním počítačům za běhu (JIT)](just-in-time-explained.md)
* [Zabezpečení clusterů Azure Kubernetes](defender-for-kubernetes-introduction.md)