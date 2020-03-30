---
title: Zabezpečené skóre v Azure Security Center
description: Popis zabezpečeného skóre Azure Security Center a jeho ovládacích prvků zabezpečení
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2020
ms.author: memildin
ms.openlocfilehash: d7eea9cd83e72b6ffeaae319a8e87c065015e6b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415778"
---
# <a name="enhanced-secure-score-preview-in-azure-security-center"></a>Vylepšené skóre zabezpečení (preview) v Azure Security Center

## <a name="introduction-to-secure-score"></a>Úvod k zajištění skóre

Azure Security Center má dva hlavní cíle: pomoci vám pochopit aktuální situaci zabezpečení a efektivně a efektivně zlepšit zabezpečení. Ústředním aspektem Security Center, který vám umožní dosáhnout těchto cílů, je bezpečné skóre.

Centrum zabezpečení průběžně vyhodnocuje vaše prostředky, předplatná a organizaci z důvodu problémů se zabezpečením. Poté agreguje všechna zjištění do jednoho skóre, takže můžete na první pohled zjistit aktuální bezpečnostní situaci: čím vyšší je skóre, tím nižší je zjištěná úroveň rizika. Skóre můžete použít ke sledování úsilí o zabezpečení a projektů ve vaší organizaci. 

Stránka zabezpečeného skóre centra zabezpečení obsahuje:

- **Skóre** - Zabezpečené skóre je zobrazeno jako procentuální hodnota, ale základní hodnoty jsou také jasné:

    [![Zabezpečené skóre zobrazené jako procentuální hodnota s jasnými podkladovými čísly](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **Ovládací prvky zabezpečení** – každý ovládací prvek je logickou skupinou souvisejících doporučení zabezpečení a odráží ohrožené povrchy útoku. Ovládací prvek je sada doporučení zabezpečení s pokyny, které vám pomohou implementovat tato doporučení. Vaše skóre se zlepší pouze při nápravě *všech* doporučení pro jeden prostředek v rámci ovládacího prvku.

    Chcete-li okamžitě zjistit, jak dobře vaše organizace zabezpečuje jednotlivé prostory pro útok, zkontrolujte skóre pro každý ovládací prvek zabezpečení.

    Další informace naleznete [v tématu Jak se počítá skóre zabezpečení](secure-score-security-controls.md#how-the-secure-score-is-calculated) níže. 


>[!TIP]
> Dřívější verze Centra zabezpečení získaly body na úrovni doporučení: při nápravě doporučení pro jeden prostředek se zlepšilo vaše skóre zabezpečení. Skóre se dnes zlepší pouze v případě, že napravíte *všechna* doporučení pro jeden prostředek v rámci ovládacího prvku. Skóre se tedy zlepší pouze v případě, že jste zlepšili zabezpečení prostředku.
> Zatímco tato vylepšená verze je stále ve verzi Preview, dřívější prostředí zabezpečenéskóre je k dispozici jako možnost z webu Azure Portal. 


## <a name="locating-your-secure-score"></a>Vyhledání bezpečného skóre

Security Center zobrazuje vaše skóre výrazně: je to první věc zobrazená na stránce Přehled. Pokud kliknete na vyhrazenou stránku zabezpečeného skóre, uvidíte skóre rozdělené podle předplatného. Kliknutím na jedno předplatné zobrazíte podrobný seznam doporučení s prioritou a potenciální dopad jejich nápravy na skóre předplatného.

## <a name="how-the-secure-score-is-calculated"></a>Způsob výpočtu zabezpečeného skóre 

Příspěvek každé bezpečnostní kontroly k celkovému bezpečnému skóre je jasně uveden na stránce doporučení.

[![Rozšířené zabezpečené skóre zavádí bezpečnostní kontroly](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Chcete-li získat všechny možné body pro ovládací prvek zabezpečení, všechny vaše prostředky musí splňovat všechna doporučení zabezpečení v rámci ovládacího prvku zabezpečení. Centrum zabezpečení má například několik doporučení týkajících se zabezpečení portů pro správu. V minulosti jste mohli napravit některé z těchto souvisejících a vzájemně závislých doporučení, zatímco ostatní nevyřešené a vaše bezpečné skóre by se zlepšilo. Když se na to podíváme objektivně, je snadné tvrdit, že se vaše bezpečnost nezlepšila, dokud jste je všechny nevyřešili. Nyní je musíte všechny napravit, abyste změnili své bezpečné skóre.

Například ovládací prvek zabezpečení s názvem "Použít aktualizace systému" má maximální skóre šest bodů, které můžete vidět v popisku na potenciální zvýšení hodnoty ovládacího prvku:

[![Ovládací prvek zabezpečení "Použít aktualizace systému"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

Potenciál pro bezpečnostní řízení "Použít aktualizace systému" na obrázku výše ukazuje "2% (1 Point)". To znamená, že pokud napravíte všechna doporučení v tomto ovládacím prvku, vaše skóre se zvýší o 2% (v tomto případě jeden bod). Pro jednoduchost jsou hodnoty ve sloupci "Potenciální zvýšení" seznamu doporučení zaokrouhleny na celá čísla. Popisky ukazují přesné hodnoty:

* **Maximální skóre** - Maximální počet bodů, které můžete získat vyplněním všech doporučení v rámci ovládacího prvku. Maximální skóre pro ovládací prvek označuje relativní význam tohoto ovládacího prvku. Použijte maximální hodnoty skóre k třídění problémů, na kterých chcete pracovat jako první. 
* **Potenciální zvýšení** - zbývající body, které máte k dispozici v rámci ovládacího prvku. Chcete-li získat tyto body přidány do zabezpečené skóre, napravit všechna doporučení ovládacího prvku. Ve výše uvedeném příkladu je jeden bod zobrazený pro ovládací prvek ve skutečnosti 0,96 bodu.
* **Aktuální skóre** - aktuální skóre pro tento ovládací prvek. Každá kontrola přispívá k celkovému skóre. V tomto příkladu ovládací prvek přispívá 5,04 bodů k celkovému součtu. 

### <a name="calculations---understanding-your-score"></a>Výpočty - pochopení vašeho skóre

|Metrika|Vzorec a příklad|
|-|-|
|**Aktuální skóre ovládacího prvku zabezpečení**|<br>![Rovnice pro výpočet aktuálního skóre ovládacího prvku zabezpečení](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Každá jednotlivá kontrola zabezpečení přispívá ke skóre zabezpečení. Každý prostředek ovlivněný doporučením v rámci ovládacího prvku přispívá k aktuálnímu skóre ovládacího prvku. Aktuální skóre pro každý ovládací prvek je míra stavu zdrojů *v rámci* ovládacího prvku.<br>![Popisky zobrazující hodnoty použité při výpočtu aktuálního skóre ovládacího prvku zabezpečení](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>V tomto příkladu maximální skóre 6 by být rozdělena 78, protože to je součet v pořádku a není v pořádku prostředky.<br>6 / 78 = 0,0769<br>Vynásobením počtem zdravých zdrojů (4) se vyčte aktuální skóre:<br>0,0769 * 4 = **0,31**<br><br>|
|**Bezpečné skóre**<br>Jedno předplatné|<br>![Rovnice pro výpočet aktuálního zabezpečeného skóre](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Zabezpečené skóre s jedním předplatným s povolenými všemi ovládacími prvky](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>V tomto příkladu je k dispozici jedno předplatné se všemi ovládacími prvky zabezpečení (potenciální maximální skóre 60 bodů). Skóre ukazuje 28 bodů z možných 60 a zbývajících 32 bodů se odráží v údajích "Potenciální zvýšení skóre" bezpečnostních kontrol.<br>![Seznam kontrol a potenciální zvýšení skóre](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Bezpečné skóre**<br>Více předplatných|<br>Aktuální skóre pro všechny prostředky ve všech předplatných jsou přidány a výpočet je pak stejný jako pro jedno předplatné<br><br>Při zobrazení více předplatných vyhodnotí zabezpečené skóre všechny prostředky v rámci všech povolených zásad a seskupí jejich kombinovaný dopad na maximální skóre každého ovládacího prvku zabezpečení.<br>![Zabezpečené skóre pro více předplatných s povolenými všemi ovládacími prvky](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Kombinované skóre **není** průměr; spíše je to vyhodnocený postoj stavu všech prostředků ve všech předplatných.<br>I zde, pokud přejdete na stránku doporučení a sečtete potenciální dostupné body, zjistíte, že je to rozdíl mezi aktuálním skóre (24) a maximálním dostupným skóre (60).|
||||

## <a name="improving-your-secure-score"></a>Zlepšení bezpečného skóre

Chcete-li zlepšit své skóre zabezpečení, navažte doporučení zabezpečení ze seznamu doporučení. Každé doporučení můžete opravit ručně pro každý prostředek nebo pomocí **rychlé opravy!** možnost (pokud je k dispozici) rychle použít nápravu pro doporučení pro skupinu zdrojů. Další informace naleznete [v tématu Nápravná doporučení](security-center-remediate-recommendations.md).

>[!IMPORTANT]
> Pouze vestavěná doporučení mají vliv na zabezpečené skóre.

## <a name="security-controls-and-their-recommendations"></a>Bezpečnostní kontroly a jejich doporučení

V následující tabulce jsou uvedeny ovládací prvky zabezpečení v Centru zabezpečení Azure. Pro každý ovládací prvek můžete zobrazit maximální počet bodů, které můžete přidat do zabezpečeného skóre, pokud napravíte *všechna* doporučení uvedená v ovládacím prvku pro *všechny* vaše prostředky. 

> [!TIP]
> Pokud chcete tento seznam filtrovat nebo řadit jinak, zkopírujte ho a vložte do Excelu.

|Ovládací prvek zabezpečení|Maximální počet bodů bezpečného skóre|Doporučení|
|----------------|:-------------------:|---------------|
|**Povolení MFA**|10|- Vícefaktorové finanční informace by měly být povoleny na účtech s oprávněními vlastníka k vašemu předplatnému<br>- MFA by měla být povolena na účtech s oprávněním ke čtení na vašem předplatném<br>- MFA by měly být povoleny účty s oprávněním k zápisu na vaše předplatné|
|**Bezpečné porty pro správu**|8|- Just-In-Time řízení přístupu k síti by měla být použita na virtuálních počítačích<br>- Virtuální počítače by měly být přidruženy ke skupině zabezpečení sítě<br>- Porty pro správu by měly být uzavřeny na virtuálních počítačích|
|**Instalace aktualizací systému**|6|- Sledování zdravotních problémů agentů by mělo být vyřešeno na vašich počítačích<br>- Monitorovací agent by měl být nainstalován na škálovacísady virtuálních strojů<br>- Monitorovací činidlo by mělo být instalováno na vašich strojích<br>- Verze operačního systému by měla být aktualizována pro vaše role cloudových služeb<br>- Aktualizace systému na škálovacísady virtuálních strojů by měly být nainstalovány<br>- Aktualizace systému by měly být instalovány na vašich počítačích<br>- Vaše stroje by měly být restartovány, aby se vztahovaly aktualizace systému<br>- Kubernetesovy služby by měly být upgradovány na nezranitelnou verzi Kubernetes<br>- Monitorovací agent by měl být nainstalován na virtuálních počítačích|
|**Náprava ohrožení zabezpečení**|6|- Pokročilé zabezpečení dat by mělo být povoleno na vašich SQL serverech<br>- Chyby zabezpečení v ibi registru kontejnerů Azure by měly být opraveny<br>- Zranitelnosti ve vašich DATABÁZÍCH SQL by měly být opraveny<br>- Chyby zabezpečení by měly být opraveny řešením pro posouzení zranitelnosti<br>- Posouzení zranitelnosti by mělo být povoleno na vašich instancích spravovaných SQL<br>- Posouzení zranitelnosti by mělo být povoleno na vašich SQL serverech<br>- Řešení pro posouzení zranitelnosti by mělo být nainstalováno na virtuálních počítačích|
|**Povolení šifrování v klidovém stavu**|4|- Šifrování disku by mělo být použito na virtuálních počítačích<br>- Transparentní šifrování dat v databázích SQL by mělo být povoleno<br>- Proměnné automatizačního účtu by měly být šifrovány<br>- Clustery Service Fabric by měly mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.<br>- SQL server TDE chránič by měl být šifrován s vlastním klíčem|
|**Šifrování dat při přenosu**|4|- API aplikace by měla být přístupná pouze přes HTTPS<br>- Aplikace funkce by měla být přístupná pouze přes HTTPS<br>- Měla by být povolena pouze zabezpečená připojení k redis cache<br>- Bezpečný přenos do účtů úložiště by měl být povolen<br>- Webová aplikace by měla být přístupná pouze přes HTTPS|
|**Správa přístupu a oprávnění**|4|- Pro vaše předplatné by měli být určeni maximálně 3 vlastníci.<br>- Zastaralé účty by měly být odstraněny z vašeho předplatného (Náhled)<br>- Zastaralé účty s oprávněními vlastníka by měly být odstraněny z vašeho předplatného (Náhled)<br>- Externí účty s oprávněními vlastníka by měly být odstraněny z vašeho předplatného (Náhled)<br>- Externí účty s oprávněním ke čtení by měly být odebrány z vašeho předplatného<br>- Externí účty s oprávněním k zápisu by měly být odstraněny z vašeho předplatného (Náhled)<br>- K vašemu předplatnému by mělo být přiřazeno více než jednoho vlastníka.<br>- Řízení přístupu na základě rolí (RBAC) by mělo být použito ve službách Kubernetes Services (Preview)<br>- Clustery Service Fabric by měly používat azure active directory jenom pro ověřování klientů.|
|**Náprava konfigurací zabezpečení**|4|- Pod bezpečnostní politiky by měly být definovány na Kubernetes služby<br>- Chyby zabezpečení v konfiguracích zabezpečení kontejnerů by měly být opraveny<br>- Chyby zabezpečení v konfiguraci zabezpečení na vašich počítačích by měly být opraveny<br>- Chyby zabezpečení v konfiguraci zabezpečení na škálovacísady virtuálních počítačů by měly být opraveny<br>- Monitorovací agent by měl být nainstalován na virtuálních počítačích<br>- Monitorovací činidlo by mělo být instalováno na vašich strojích<br>- Monitorovací agent by měl být nainstalován na škálovacísady virtuálních strojů<br>- Sledování zdravotních problémů agentů by mělo být vyřešeno na vašich počítačích|
|**Omezení neoprávněného přístupu k síti**|4|- PŘEDÁVÁNÍ IP adres na vašem virtuálním počítači by mělo být zakázáno<br>- Autorizované ROZSAHY IP by měly být definovány na Kubernetes Services (Preview)<br>- (ZASTARALÉ) Přístup ke službám aplikace by měl být omezen (Preview)<br>- (DEPRECATED) Pravidla pro webové aplikace na nevládních<br>- Virtuální počítače by měly být přidruženy ke skupině zabezpečení sítě<br>- CORS by neměla umožnit každému prostředku přístup k aplikaci API<br>- CORS by neměla umožnit každému zdroji přístup k vaší funkci App<br>- CORS by neměla umožnit každému zdroji přístup k webové aplikaci<br>- Vzdálené ladění by mělo být vypnuto pro aplikaci API<br>- Vzdálené ladění by mělo být vypnuto pro funkci App<br>- Vzdálené ladění by mělo být vypnuto pro webovou aplikaci<br>- Přístup by měl být omezen pro tolerantní skupiny zabezpečení sítě s virtuálními virtuálními servery orientovanými na internet.<br>- Network Security Group Pravidla pro internet čelí virtuální počítače by měly být posíleny|
|**Použití adaptivního řízení aplikací**|3|- Adaptivní kontroly aplikací by měly být povoleny na virtuálních počítačích<br>- Monitorovací agent by měl být nainstalován na virtuálních počítačích<br>- Monitorovací činidlo by mělo být instalováno na vašich strojích<br>- Sledování zdravotních problémů agentů by mělo být vyřešeno na vašich počítačích|
|**Použít klasifikaci dat**|2|- Citlivá data ve vašich SQL databázích by měla být klasifikována (Preview)|
|**Ochrana aplikací před útoky DDoS**|2|- DDoS Protection Standard by měla být povolena|
|**Povolení ochrany koncového bodu**|2|- Selhání stavu ochrany koncových bodů by měla být opravena na škálovacích sadách virtuálních strojů<br>- Na vašich počítačích by měly být vyřešeny zdravotní problémy s ochranou koncových bodů<br>- Řešení ochrany koncového bodu by mělo být instalováno na škálovacích sadách virtuálních strojů<br>- Instalace řešení ochrany koncového bodu na virtuálních počítačích<br>- Sledování zdravotních problémů agentů by mělo být vyřešeno na vašich počítačích<br>- Monitorovací agent by měl být nainstalován na škálovacísady virtuálních strojů<br>- Monitorovací činidlo by mělo být instalováno na vašich strojích<br>- Monitorovací agent by měl být nainstalován na virtuálních počítačích<br>- Instalace řešení ochrany koncového bodu na vaše stroje|
|**Povolení auditování a protokolování**|1|- Auditování na SERVERU SQL by mělo být povoleno<br>- Diagnostické protokoly ve službách app by měly být povoleny<br>- Diagnostické protokoly v Azure Data Lake Store by měly být povoleny<br>- Diagnostické protokoly v Azure Stream Analytics by měly být povoleny<br>- Diagnostické protokoly v dávkových účtech by měly být povoleny.<br>- Diagnostické protokoly v Data Lake Analytics by měly být povoleny<br>- Diagnostické protokoly v centru událostí by měly být povoleny.<br>- Diagnostické protokoly v ioT hubu by měly být povoleny<br>- Diagnostické protokoly v trezoru klíčů by měly být povoleny<br>- Diagnostické protokoly v Logic Apps by měly být povoleny<br>- Diagnostické protokoly ve vyhledávací službě by měly být povoleny.<br>- Diagnostické protokoly v service bus by měly být povoleny<br>- Diagnostické protokoly ve škálovacích sadách virtuálních strojů by měly být povoleny.<br>- Pravidla upozornění metriky by měla být konfigurována na dávkových účtech.<br>- Nastavení auditování SQL by mělo mít nakonfigurováno akční skupiny pro zachycení kritických aktivit<br>- SQL servery by měly být konfigurovány s auditováním retenčních dnů delšínež 90 dní.|
|**Implementace osvědčených postupů zabezpečení**|0|- Přístup k účtům úložiště s firewallem a konfiguracemi virtuálních sítí by měl být omezen<br>- Všechna autorizační pravidla kromě RootManageSharedAccessKey by měla být odebrána z oboru názvů centra událostí.<br>- Správce služby Azure Active Directory by měl být zřízen pro servery SQL.<br>- Pravidla autorizace instance Event Hub by měla být definována.<br>– Účty úložiště by se měly migrovat do nových prostředků Azure Resource Manageru.<br>- Virtuální počítače by měly být migrovány do nových prostředků Azure Resource Manager<br>- Rozšířené nastavení zabezpečení dat pro SQL server by měl obsahovat e-mailovou adresu pro příjem výstrah zabezpečení<br>- Rozšířené zabezpečení dat by mělo být povoleno na spravovaných instancích<br>- Všechny pokročilé typy ochrany před hrozbami by měly být povoleny v rozšířeném nastavení zabezpečení dat spravované SQL.<br>- E-mailová oznámení správcům a vlastníkům předplatného by měla být povolena v pokročilých nastaveních zabezpečení dat serveru SQL<br>- Rozšířené typy ochrany před hrozbami by měly být nastaveny na 'Vše' v SQL server Advanced Data Security nastavení<br>- Podsítě by měly být přidruženy ke skupině zabezpečení sítě.<br>- Všechny pokročilé typy ochrany před hrozbami by měly být povoleny v rozšířeném nastavení zabezpečení dat serveru SQL server|
||||

## <a name="secure-score-faq"></a>Nejčastější dotazy k zabezpečenému skóre

### <a name="why-has-my-secure-score-gone-down"></a>Proč se moje bezpečné skóre snížilo?
Security Center přešel na rozšířené zabezpečené skóre (aktuálně ve stavu náhledu), které zahrnuje změny ve způsobu výpočtu skóre. Nyní je nutné vyřešit všechna doporučení pro zdroj přijímat body. Skóre se také změnilo na stupnici 0-10.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Pokud v ovládacím prvku zabezpečení řeším pouze tři ze čtyř doporučení, změní se moje zabezpečené skóre?
Ne. Nezmění se, dokud nenanápravné všechny doporučení pro jeden prostředek. Chcete-li získat maximální skóre pro ovládací prvek, je nutné napravit všechna doporučení pro všechny zdroje.

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>Je předchozí zkušenost s bezpečným skóre stále k dispozici? 
Ano. Na chvíli budou běhat bok po boku, aby usnadnili přechod. Očekávejte, že předchozí model bude postupně vyřazen v čase. 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Pokud se na mě doporučení nevztahuje a zakážu ho v zásadách, bude moje kontrola zabezpečení splněna a moje zabezpečené skóre bude aktualizováno?
Ano. Doporučujeme zakázat doporučení, pokud jsou nepoužitelná ve vašem prostředí. Pokyny k zakázání konkrétního doporučení naleznete v tématu [Disable security policies](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Pokud mi bezpečnostní kontrola nabízí nula bodů k mému zabezpečenému skóre, mám ho ignorovat?
V některých případech uvidíte maximální skóre ovládacího prvku větší než nula, ale dopad je nulový. Pokud je přírůstkové skóre pro opravu prostředků zanedbatelné, je zaokrouhleno na nulu. Tato doporučení neignorujte, protože stále přinášejí vylepšení zabezpečení. Jedinou výjimkou je ovládací prvek "Další osvědčené postupy". Náprava těchto doporučení nezvýší vaše skóre, ale zvýší vaše celkové zabezpečení.

## <a name="next-steps"></a>Další kroky

Tento článek popisuje skóre zabezpečení a ovládací prvky zabezpečení, které zavádí. Související materiály naleznete v následujících článcích:

- [Informace o různých prvcích doporučení](security-center-recommendations.md)
- [Přečtěte si, jak napravit doporučení](security-center-remediate-recommendations.md)