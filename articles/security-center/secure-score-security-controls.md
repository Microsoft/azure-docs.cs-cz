---
title: Rozšířené zabezpečené skóre (Preview) v Azure Security Center
description: Popis rozšířeného zabezpečeného skóre (Preview) a ovládacích prvků zabezpečení v Azure Security Center
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
ms.date: 12/04/2019
ms.author: memildin
ms.openlocfilehash: ffe9ea5f46571f6a22717c376c97055f6f1759e4
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604719"
---
# <a name="the-enhanced-secure-score-preview"></a>Rozšířené skóre zabezpečení (Preview) 

Tento článek představuje rozšířené zabezpečené skóre (aktuálně ve verzi Preview), doprovodné kontrolní mechanismy zabezpečení a výhody, které přináší. Vysvětluje také, jak se počítá vaše skóre.

## <a name="introduction-to-secure-score"></a>Úvod do zabezpečeného skóre

Azure Security Center má dva hlavní cíle: a pomůže vám pochopit aktuální situaci v zabezpečení a pomáhat vám efektivně a efektivně zlepšit zabezpečení. Ústřední aspekt Security Center, který vám umožňuje dosáhnout těchto cílů, je bezpečné skóre.

Security Center průběžně vyhodnocuje vaše prostředky, odběry a organizace pro problémy se zabezpečením. Pak agreguje všechna zjištění do jediného skóre, abyste na první pohled mohli sdělit aktuální situaci zabezpečení: čím vyšší je skóre, tím se sníží zjištěná úroveň rizika. Pomocí skóre můžete sledovat úsilí a projekty zabezpečení ve vaší organizaci. 

*Rozšířené* zabezpečené skóre (aktuálně ve verzi Preview) je **zaměřeno** na oblast útoku a přináší tři výhody:

- **Bezpečnostní prvky zabezpečení** – doporučení zabezpečení se teď seskupují do logických sad, které lépe odráží vaše zranitelné plochy pro útok. Další informace najdete v tématu [jak se vypočítává zabezpečené skóre](secure-score-security-controls.md#how-the-secure-score-is-calculated) .

- **Celkové skóre lépe odráží celkový** počet bodů stav, které byly zadány na úrovni doporučení. S tímto vylepšením se skóre vylepšit jenom při nápravě *všech* doporučení pro jeden prostředek v rámci ovládacího prvku. To znamená, že vaše skóre se zlepšuje jenom v případě, že se zvyšuje zabezpečení prostředku. 

- **Stav zabezpečení jednotlivých ploch útoku je viditelný** – zobrazení skóre na základě řízení zabezpečení se stává místem, kde můžete získat detailní přehled o tom, jak dobře vaše organizace zabezpečuje jednotlivé plochy pro útok.

Rozšířené zabezpečené skóre je zobrazeno jako procento, jak je znázorněno na následujícím snímku obrazovky:

[![zvýšeného zabezpečeného skóre (Preview) teď obsahuje procento.](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)


## <a name="locating-your-secure-score"></a>Vyhledání zabezpečeného skóre

Security Center zobrazuje vaše skóre v dobrém prostředí: je to první věc zobrazená na stránce Přehled. Pokud kliknete na stránku vyhrazeného zabezpečeného skóre, zobrazí se skóre, které je rozdělené podle předplatného. Kliknutím na jedno předplatné zobrazíte podrobný seznam doporučení s upřednostněním a potenciální dopad, který by oprava na skóre předplatného.

## <a name="how-the-secure-score-is-calculated"></a>Jak se počítá zabezpečené skóre 

Před touto verzí Preview Security Center považovat každé doporučení jednotlivě a podle jeho závažnosti jim přiřadí hodnotu. Týmy zabezpečení, které pracují na zlepšování zabezpečení stav, musely upřednostnit odpovědi na Security Center doporučení na základě úplného seznamu zjištění. Pokaždé, když opravíte doporučení pro jeden prostředek, zlepší se vaše zabezpečené skóre.

V rámci vylepšení zabezpečeného skóre se teď doporučení seskupují do **ovládacích prvků zabezpečení**. Ovládací prvek je sada doporučení zabezpečení a pokyny, které vám pomůžou s implementací těchto doporučení. Ovládací prvky jsou logické seskupení souvisejících doporučení. Body již nejsou poskytovány na úrovni doporučení. Místo toho se skóre vylepšit jenom při nápravě *všech* doporučení pro jeden prostředek v rámci ovládacího prvku.

Na stránce doporučení se jasně zobrazuje příspěvek každého řízení bezpečnosti k celkovému zabezpečenému skóre.

[![zvýšeného zabezpečeného skóre (Preview) zavádí ovládací prvky zabezpečení.](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Abyste získali všechny možné body pro řízení zabezpečení, všechny vaše prostředky musí splňovat všechna doporučení zabezpečení v rámci řízení bezpečnosti. Security Center například obsahuje více doporučení týkajících se zabezpečení portů pro správu. V minulosti byste mohli opravit některá z těchto souvisejících a vzájemně závislých doporučení a zároveň nechat ostatní nevyřešené a vaše zabezpečené skóre by se vylepšilo. Když se podíváte na objektivně, snadno se tvrdí, že se zabezpečení jste, dokud je nevyřešili vše. Teď je potřeba je opravit tak, aby byl rozdíl pro vaše zabezpečené skóre.

Například řízení zabezpečení s názvem použít aktualizace systému má maximální skóre šesti bodů, které vidíte v popisku na možné zvýšení hodnoty ovládacího prvku:

[![řízení zabezpečení použít aktualizace systému](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

Potenciál ovládacího prvku zabezpečení "použít aktualizace systému" na snímku obrazovky výše ukazuje "2% (1 bod)". To znamená, že pokud opravíte všechna doporučení v tomto ovládacím prvku, vaše skóre se zvýší o 2% (v tomto případě jeden bod). V zájmu jednoduchosti jsou hodnoty ve sloupci "potenciální zvýšení" seznamu doporučení zaokrouhleny na celá čísla. Popisky obsahují přesné hodnoty:

* **Maximální skóre** – maximální počet bodů, které můžete získat provedením všech doporučení v rámci ovládacího prvku. Maximální skóre pro ovládací prvek označuje relativní význam tohoto ovládacího prvku. Použijte hodnoty maximálního skóre k určení, které problémy se mají nejdřív pracovat. 
* **Možné zvýšení** – zbývající body, které jsou k dispozici v rámci ovládacího prvku. Chcete-li tyto body přidat do zabezpečeného skóre, opravte všechna doporučení ovládacího prvku. V předchozím příkladu je jeden bod zobrazený pro ovládací prvek ve skutečnosti 0,96 bodů.
* **Aktuální skóre** – aktuální skóre tohoto ovládacího prvku. Každý ovládací prvek přispívá k celkovému skóre. V tomto příkladu je ovládací prvek přispívající k celkovému součtu 5,04 bodů. 

### <a name="calculations---understanding-your-score"></a>Výpočty – princip skóre

|Metrika|Vzorec a příklad|
|-|-|
|**Aktuální skóre ovládacího prvku zabezpečení**|<br>![rovnice pro výpočet aktuálního skóre ovládacího prvku zabezpečení](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Každé individuální řízení zabezpečení přispívá ke skóre zabezpečení. Každý prostředek, na který má vliv doporučení v rámci ovládacího prvku, přispívá k aktuálnímu skóre ovládacího prvku. Aktuální skóre pro každý ovládací prvek je míra stavu prostředků *v rámci* ovládacího prvku.<br>![popisy zobrazení hodnot použitých při výpočtu aktuálního skóre ovládacího prvku zabezpečení](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>V tomto příkladu je maximální skóre 6 děleno 78, protože to je součet zdravých a špatných prostředků.<br>6/78 = 0,0769<br>Vynásobení tohoto počtu zdravých prostředků (4) má za následek aktuální skóre:<br>0,0769 * 4 = **0,31**<br><br>|
|**Bezpečnostní skóre**<br>Jedno předplatné|<br>![Rovnice pro výpočet aktuálního zabezpečeného skóre](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Zabezpečené skóre jednotného předplatného se všemi povolenými ovládacími prvky](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>V tomto příkladu je k dispozici jedno předplatné se všemi dostupnými ovládacími prvky zabezpečení (potenciální maximální skóre 60 bodů). Skóre zobrazuje 28 bodů z možného 60 a zbývající 32 body se projeví v hodnotách "potenciálního nárůstu" v rámci ovládacích prvků zabezpečení.<br>![Seznam ovládacích prvků a možné zvýšení skóre](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Bezpečnostní skóre**<br>Několik předplatných|<br>Přidávají se aktuální skóre pro všechny prostředky ve všech předplatných a výpočet je stejný jako u jednoho předplatného.<br><br>Při prohlížení více předplatných vyhodnotí zabezpečené skóre všechny prostředky ve všech povolených zásadách a seskupují jejich kombinovaný dopad na maximální skóre každého ovládacího prvku zabezpečení.<br>![bezpečné skóre pro více předplatných se všemi povolenými ovládacími prvky](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Kombinované skóre **nepředstavuje průměr** ; místo toho se jedná o vyhodnocený stav stavu všech prostředků ve všech předplatných.<br>Pokud se zobrazí stránka doporučení a přidáte potenciální dostupné body, zjistíte, že se jedná o rozdíl mezi aktuálním skóre (24) a maximálním dostupným skóre (60).|
||||

## <a name="improving-your-secure-score"></a>Vylepšení zabezpečeného skóre

Pokud chcete vylepšit vaše zabezpečené skóre, napravte doporučení zabezpečení ze seznamu doporučení. Každé doporučení můžete opravit ručně pro každý prostředek nebo pomocí **rychlé opravy.** možnost (Pokud je dostupná), pokud chcete použít nápravu pro doporučení na skupinu prostředků rychle. Další informace najdete v tématu o [opravách doporučení](security-center-remediate-recommendations.md).

Bezpečnostní skóre mají dopad jenom na integrovaná doporučení.

## <a name="security-controls-and-their-recommendations"></a>Ovládací prvky zabezpečení a jejich doporučení

Následující tabulka uvádí ovládací prvky zabezpečení v Azure Security Center. U každého ovládacího prvku uvidíte maximální počet bodů, které můžete přidat do svého zabezpečeného skóre, pokud opravíte *všechna* doporučení uvedená v ovládacím prvku pro *všechny* vaše prostředky. 

> [!TIP]
> Pokud byste chtěli tento seznam filtrovat nebo seřadit jinak, zkopírujte ho a vložte ho do Excelu.

|Řízení zabezpečení|Maximální počet zabezpečených bodů skóre|Doporučení|
|----------------|:-------------------:|---------------|
|**Povolení MFA**|10|– Vícefaktorové ověřování by mělo být povolené u účtů s oprávněním vlastníka u vašeho předplatného.<br>-Vícefaktorové ověřování by mělo být povolené u účtů s oprávněním ke čtení u vašeho předplatného.<br>– Vícefaktorové ověřování by mělo být povolené pro účty s oprávněním k zápisu v předplatném|
|**Porty zabezpečené správy**|8|– Na virtuálních počítačích by se mělo použít řízení přístupu k síti za běhu<br>– Virtuální počítače by měly být přidružené ke skupině zabezpečení sítě.<br>– Porty pro správu by se měly na virtuálních počítačích zavřít.|
|**Instalace aktualizací systému**|6|– Problémy se stavem agenta monitorování by se měly vyřešit na vašich počítačích<br>– Agent monitorování by měl být nainstalovaný na Virtual Machine Scale Sets.<br>– Agent monitorování by měl být nainstalovaný na vašich počítačích.<br>– Pro role cloudové služby by se měla aktualizovat verze operačního systému<br>-Měly by se instalovat aktualizace systému pro sady škálování virtuálních počítačů.<br>– Aktualizace systému by se měly nainstalovat na vaše počítače<br>– Počítače je potřeba restartovat, aby se projevily systémové aktualizace.<br>-Kubernetes služby by měly být upgradovány na nezranitelnou Kubernetes verzi<br>– Agent monitorování by měl být nainstalovaný na vašich virtuálních počítačích.|
|**Náprava ohrožení zabezpečení**|6|– Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat<br>-V Azure Container Registry imagí by měly být opraveny ohrožení zabezpečení (Preview)<br>-V databázích SQL by měly být opraveny ohrožení zabezpečení<br>-Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.<br>-Na spravovaných instancích SQL by mělo být povolené posouzení ohrožení zabezpečení<br>-Na vašich serverech SQL by mělo být povolené posouzení ohrožení zabezpečení<br>– Řešení posouzení ohrožení zabezpečení by se mělo nainstalovat na virtuální počítače.|
|**Povolit šifrování v klidovém umístění**|4|-Na virtuálních počítačích by se mělo použít šifrování disku<br>-Transparentní šifrování dat pro databáze SQL by měly být povolené<br>– Proměnné účtu Automation by se měly šifrovat.<br>-Service Fabric clustery by měly mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign<br>– Ochrana TDE SQL serveru by se měla šifrovat pomocí vlastního klíče.|
|**Šifrovat data při přenosu**|4|-API aplikace by měla být přístupná jenom přes HTTPS<br>-Function App by měly být přístupné jenom přes HTTPS<br>-Měla by být povolená jenom zabezpečená připojení k vašemu Redis Cache.<br>– Mělo by se povolit zabezpečený přenos do účtů úložiště.<br>-Webová aplikace by měla být přístupná jen přes protokol HTTPS|
|**Správa přístupu a oprávnění**|4|– Pro vaše předplatné by se měly určit maximálně 3 vlastníci.<br>– Zastaralé účty by se měly odebírat z předplatného (Preview)<br>– Zastaralé účty s oprávněním vlastníka by se měly odebírat z předplatného (Preview).<br>– Externí účty s oprávněním vlastníka by se měly odebírat z předplatného (Preview).<br>– Externí účty s oprávněním ke čtení by se měly z vašeho předplatného odebrat.<br>– Externí účty s oprávněním k zápisu by se měly odebírat z předplatného (Preview)<br>– K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.<br>– Na Kubernetes Services (Preview) by se měla používat Access Control na základě rolí (RBAC)<br>-Service Fabric clustery by se měly používat jenom Azure Active Directory pro ověřování klientů.|
|**Náprava konfigurací zabezpečení**|4|Zásady zabezpečení by měly být definované v Kubernetes Services (Preview)<br>-Ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by měla být opravena<br>– V konfiguraci zabezpečení na vašich počítačích by se měly napravit ohrožení zabezpečení.<br>– V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly opravit ohrožení zabezpečení.<br>– Agent monitorování by měl být nainstalovaný na vašich virtuálních počítačích.<br>– Agent monitorování by měl být nainstalovaný na vašich počítačích.<br>– Agent monitorování by měl být nainstalovaný na Virtual Machine Scale Sets.<br>– Problémy se stavem agenta monitorování by se měly vyřešit na vašich počítačích|
|**Omezit neautorizovaný přístup k síti**|4|-Předávání IP na virtuálním počítači by mělo být zakázané.<br>– Pro služby Kubernetes Services (Preview) by se měly definovat rozsahy autorizovaných IP adres<br>– (Nepoužívané) přístup k App Services by měl být omezený (Preview)<br>– (Nepoužívané) pravidla pro webové aplikace v IaaS skupin zabezpečení sítě by měla být Posílená<br>– Virtuální počítače by měly být přidružené ke skupině zabezpečení sítě.<br>– CORS by nemělo umožňovat každému prostředku přístup k vaší aplikaci API<br>– CORS by nemělo umožňovat každému prostředku přístup k vašemu Function App<br>– CORS by nemělo umožňovat všem prostředkům přístup k vaší webové aplikaci<br>– Pro aplikaci API by mělo být vypnuto vzdálené ladění.<br>– Vzdálené ladění by mělo být vypnuté pro Function App<br>– Vzdálené ladění by mělo být pro webovou aplikaci vypnuté.<br>– Přístup by měl být omezený na povolující skupiny zabezpečení sítě u virtuálních počítačů s přístupem k Internetu.<br>– Pravidla skupiny zabezpečení sítě pro virtuální počítače s přístupem k Internetu by měla být zesílená.|
|**Použít adaptivní řízení aplikací**|3|-Na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.<br>– Agent monitorování by měl být nainstalovaný na vašich virtuálních počítačích.<br>– Agent monitorování by měl být nainstalovaný na vašich počítačích.<br>– Problémy se stavem agenta monitorování by se měly vyřešit na vašich počítačích|
|**Použít klasifikaci dat**|2|Citlivá data v databázích SQL by měla být klasifikována (Preview)|
|**Ochrana aplikací před útoky DDoS**|2|-DDoS Protection by měla být povolená.|
|**Povolit službu Endpoint Protection**|2|– U služby Virtual Machine Scale Sets by se měla opravit selhání stavu ochrany koncového bodu Endpoint Protection<br>– Na vašich počítačích by se měly vyřešit problémy stavu ochrany koncových bodů.<br>– Řešení ochrany koncových bodů by se mělo nainstalovat na Virtual Machine Scale Sets.<br>-Instalovat řešení Endpoint Protection na virtuálních počítačích<br>– Problémy se stavem agenta monitorování by se měly vyřešit na vašich počítačích<br>– Agent monitorování by měl být nainstalovaný na Virtual Machine Scale Sets.<br>– Agent monitorování by měl být nainstalovaný na vašich počítačích.<br>– Agent monitorování by měl být nainstalovaný na vašich virtuálních počítačích.<br>– Instalace řešení Endpoint Protection na počítače|
|**Povolit auditování a protokolování**|1|-Auditování na SQL serveru by mělo být povolené.<br>-Protokoly diagnostiky v App Services by měly být povolené<br>-Protokoly diagnostiky v Azure Data Lake Store by měly být povolené<br>-Protokoly diagnostiky v Azure Stream Analytics by měly být povolené<br>-Protokoly diagnostiky v účtech Batch by měly být povolené.<br>-Protokoly diagnostiky v Data Lake Analytics by měly být povolené<br>-Měly by být povolené diagnostické protokoly v centru událostí.<br>-Protokoly diagnostiky v IoT Hub by měly být povolené<br>-Protokoly diagnostiky v Key Vault by měly být povolené<br>-Protokoly diagnostiky v Logic Apps by měly být povolené<br>-Protokoly diagnostiky ve službě Search Service by měly být povolené.<br>-Protokoly diagnostiky v Service Bus by měly být povolené<br>-Protokoly diagnostiky v Virtual Machine Scale Sets by měly být povolené<br>– Pravidla upozornění na metriky by se měla nakonfigurovat na účtech Batch.<br>– Nastavení auditování SQL by mělo mít akce – skupiny konfigurované pro zachycení kritických aktivit<br>– SQL servery by měly být nakonfigurované s uchováním dat po dobu delší než 90 dní.|
|**Implementace osvědčených postupů zabezpečení**|0|– Přístup k účtům úložiště pomocí brány firewall a konfigurací virtuální sítě by měl být omezený.<br>-Všechna autorizační pravidla s výjimkou RootManageSharedAccessKey by měla být odebrána z oboru názvů centra událostí<br>– Správce Azure Active Directory by měl zřídit pro SQL servery.<br>– Měla by být definovaná autorizační pravidla v instanci centra událostí.<br>– Účty úložiště by se měly migrovat na nové prostředky Azure Resource Manager.<br>– Virtuální počítače by se měly migrovat na nové prostředky Azure Resource Manager.<br>– Pokročilá nastavení zabezpečení dat pro SQL Server by měla obsahovat e-mailovou adresu pro příjem výstrah zabezpečení.<br>– Na spravovaných instancích by měla být povolená rozšířená zabezpečení dat.<br>-V rozšířených nastaveních zabezpečení dat spravované instance SQL by se měly povolit všechny rozšířené typy ochrany před hrozbami.<br>– E-mailová oznámení správcům a vlastníkům předplatného by se měla povolit v rozšířených nastaveních zabezpečení dat SQL serveru<br>-V rozšířených nastaveních zabezpečení dat SQL serveru by se měly nastavit rozšířené typy ochrany před internetovými útoky na ALL.<br>– Podsítě by měly být přidružené ke skupině zabezpečení sítě.<br>– V rozšířených nastaveních zabezpečení dat SQL serveru by se měly povolit všechny rozšířené typy ochrany před internetovými útoky.|
||||

## <a name="secure-score-faq"></a>Nejčastější dotazy k zabezpečení skóre

### <a name="why-has-my-secure-score-gone-down"></a>Proč je moje zabezpečené skóre pryč?
Změny zavedené v tomto rozšířeném bezpečnostním skóre je nutné vyřešit všemi doporučeními, aby se prostředky dostaly na body. Skóre se také změní na škálu 0-10.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Pokud v ovládacím prvku zabezpečení řeším jenom tři doporučení ze čtyř, bude se můj bezpečnostní skóre měnit?
Žádné nemění se, dokud neopravíte všechna doporučení pro jeden prostředek. Chcete-li získat maximální skóre pro ovládací prvek, je nutné opravit všechna doporučení pro všechny prostředky.

### <a name="will-this-enhanced-secure-score-replace-the-existing-secure-score"></a>Bude toto rozšířené bezpečné skóre nahrazovat stávající bezpečné skóre? 
Ano, ale v době, kdy se budou spouštět vedle sebe, usnadňují přechod.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Pokud doporučení neplatí pro mě a je v zásadách zakázané, bude řízení zabezpečení splněné a mé zabezpečené skóre se aktualizovalo?
Ano. Doporučujeme zakázat doporučení, pokud jsou ve vašem prostředí nepoužitá. Pokyny, jak zakázat konkrétní doporučení, najdete v tématu [zakázání zásad zabezpečení](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Pokud řízení zabezpečení nabízí nulové body směrem k mému zabezpečenému skóre, je vhodné je ignorovat?
V některých případech se zobrazí maximální skóre větší než nula, ale dopad je nula. Když je přírůstkové skóre pro opravu prostředků zanedbatelné, zaokrouhlí se na nulu. Tato doporučení neignorujte, protože stále přinášejí vylepšení zabezpečení. Jedinou výjimkou je ovládací prvek "další osvědčené postupy". Oprava tato doporučení nezvýší vaše skóre, ale zvýší vaše celkové zabezpečení.

## <a name="next-steps"></a>Další kroky

Tento článek popisuje zvýšené zabezpečené skóre a nové ovládací prvky zabezpečení, které zavádí. Související materiály najdete v následujících článcích:

- [Další informace o různých prvcích doporučení](security-center-recommendations.md)
- [Informace o tom, jak opravit doporučení](security-center-remediate-recommendations.md)