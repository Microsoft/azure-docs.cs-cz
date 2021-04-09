---
title: Životní cyklus podpory Azure Red Hat OpenShift
description: Pochopení životního cyklu podpory a podporovaných verzí pro Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: fca01c77a1ff47cbeee167eb408ed9f29a1307bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634324"
---
# <a name="support-lifecycle-for-azure-red-hat-openshift-4"></a>Podpora životního cyklu pro Azure Red Hat OpenShift 4

Red Hat uvolňuje drobné verze Red Hat OpenShift Container Platform (OCP) zhruba každé tři měsíce. Tyto verze obsahují nové funkce a vylepšení. Verze oprav jsou častěji časté (obvykle týdně) a jsou určeny pouze pro kritické opravy chyb v rámci dílčí verze. Tyto verze oprav můžou zahrnovat opravy chyb zabezpečení nebo hlavní chyby.

Azure Red Hat OpenShift je sestavený ze specifických verzí OCP. Tento článek se zabývá verzemi OCP, které jsou podporované pro Azure Red Hat OpenShift, a obsahuje podrobnosti o upgradech, zastaralosti a zásadách podpory.

## <a name="red-hat-openshift-versions"></a>Verze Red Hat OpenShift

Platforma Red Hat OpenShift Container Platform používá sémantickou správu verzí. Sémantická verze používá k určení různých úrovní verzí různé úrovně čísel verzí. Následující tabulka ilustruje různé části čísla sémantické verze, v tomto případě s použitím ukázkového čísla verze 4.4.11.

|Hlavní verze (x)|Vedlejší verze (y)|Oprava (z)|
|-|-|-|
|4|4|11|

Každé číslo ve verzi označuje obecnou kompatibilitu s předchozí verzí:

* **Hlavní verze**: v tuto chvíli není plánována žádná hlavní vydání verze. Hlavní verze se mění v případě nekompatibilních změn rozhraní API nebo poškození zpětné kompatibility.
* **Dílčí verze**: vydaná přibližně každé tři měsíce. Inovace podverze můžou zahrnovat přidání funkcí, vylepšení, vyřazení, odebrání, opravy chyb, vylepšení zabezpečení a další vylepšení.
* **Opravy**: obvykle jsou vydávány každý týden nebo podle potřeby. Upgrady verze opravy můžou zahrnovat opravy chyb, vylepšení zabezpečení a další vylepšení.

Zákazníci by měli mít na úmysl spustit nejnovější dílčí verzi hlavní verze, kterou používají. Pokud je například váš provozní cluster na 4,4 a 4,5 je nejnovější všeobecně dostupná dílčí verze pro 4 řady, měli byste upgradovat na 4,5, jakmile budete moct.

### <a name="upgrade-channels"></a>Kanály upgradu

Kanály pro upgrade jsou vázané na dílčí verzi Red Hat OpenShift Container Platform (OCP). Například kanály pro upgrade OCP 4,4 nikdy nebudou zahrnovat upgrade na verzi 4,5. Upgrade kanálů pouze řídí výběr vydaných verzí a neovlivňují verzi clusteru.

Azure Red Hat OpenShift 4 podporuje jenom stabilní kanály. Například: stabilní-4,4.

K upgradu z předchozí dílčí verze Azure Red Hat OpenShift můžete použít stabilní kanál-4,5. Clustery upgradované pomocí rychlých, předprodejních a kandidátských kanálů se nepodporují.

Pokud přejdete na kanál, který neobsahuje vaši aktuální verzi, zobrazí se výstraha a nebude možné doporučit žádné aktualizace, ale kdykoli se můžete v libovolném bodě kdykoli změnit zpátky na původní kanál.

## <a name="red-hat-openshift-container-platform-version-support-policy"></a>Zásada podpory pro verzi Red Hat OpenShift Container Platform

Azure Red Hat OpenShift podporuje dvě všeobecně dostupné (GA) menší verze kontejnerové platformy Red Hat OpenShift:
* Nejnovější podverze GA vydaná v Azure Red Hat OpenShift (kterou budeme označovat jako N)
* Jedna předchozí dílčí verze (N-1)

Pokud je v stabilním kanálu pro upgrade k dispozici, je možné, že jsou podporovány i novější podverze (N + 1, N + 2) v případě, že je to u nadřazeného kanálu.

Aktualizace důležitých oprav se pro clustery automaticky aplikují technici Azure Red Hat OpenShift Sites (SRE). Zákazníci, kteří chtějí instalovat aktualizace oprav předem, jsou tak zdarma.

Například pokud Azure Red Hat OpenShift zavádí 4.5. z ještě dnes, podpora je poskytována pro následující verze:

|Nová dílčí verze|Seznam podporovaných verzí|
|-|-|
|4.5. z|4.5. z, 4.4. z|

". z" je reprezentativní verze oprav. Pokud je k dispozici v stabilním kanálu pro upgrade, mohou zákazníci také upgradovat na 4.6. z.

Při zavedení nové dílčí verze je nejstarší podverze zastaralá a odebrána. Řekněme například, že aktuální seznam podporovaných verzí je 4.5. z a 4.4. z. Až se Azure Red Hat OpenShift Release 4.6. z, vydaná verze 4.4. z se odebere a nebude se podporovat do 30 dnů.

> [!NOTE]
> Upozorňujeme, že pokud zákazníci používají nepodporovanou verzi Red Hat OpenShift, můžou být při žádosti o podporu pro cluster vyzváni k upgradu. Clustery, na kterých běží Nepodporovaná vydání Red Hat OpenShift, se netýkají smlouvy SLA pro Azure Red Hat OpenShift.

## <a name="release-and-deprecation-process"></a>Proces vydání a vyřazení

V kalendáři vydaných verzí Azure Red Hat OpenShift můžete odkazovat na nadcházející verze verzí a zastaralá vydání.

Pro nové podverze kontejnerové platformy Red Hat OpenShift:
* Tým Azure Red Hat OpenShift SRE publikuje před odebráním oznámení o plánovaném datu vydání nové verze a příslušné staré verze v [poznámkách k verzi Azure Red Hat OpenShift](https://github.com/Azure/OpenShift/releases) aspoň 30 dní.
* Tým Azure Red Hat OpenShift SRE publikuje pro všechny zákazníky oznámení o stavu služby, které má přístup k Azure Red Hat OpenShift a k portálu, a pošle e-mail správcům předplatného s plánovanými datem odebrání verze.
* Odebráním verze na podporu v případě, že zákazníci dostávají podporu, mají 30 dní od odebrání verze k upgradu na podporovanou dílčí verzi.

Nové verze opravy pro kontejnerovou platformu Red Hat OpenShift:
* Z důvodu naléhavé povahy verzí oprav můžou být tyto služby zavedeny týmem Azure Red Hat OpenShift SRE, jakmile budou k dispozici.
* Obecně platí, že tým Azure Red Hat OpenShift SRE neprovádí rozsáhlou komunikaci pro instalaci nových verzí patch. Tým ale nepřetržitě monitoruje a ověřuje dostupné aktualizace, které jsou k dispozici, aby je včas podporoval. Pokud je vyžadována akce zákazníka, tým bude informovat zákazníky o upgradu.

## <a name="supported-versions-policy-exceptions"></a>Výjimky zásad podporovaných verzí

Tým Azure Red Hat OpenShift SRE si vyhrazuje právo přidat nebo odebrat nové/existující verze nebo zpozdit nadcházející verze vydaných verzí, u kterých se zjistilo, že mají minimálně jednu závažnou chybu a problémy se zabezpečením, a to bez předchozího upozornění.

Konkrétní vydání opravy mohou být vynechána nebo může být zrychlení urychleno v závislosti na závažnosti chyby nebo problému se zabezpečením.

## <a name="azure-portal-and-cli-versions"></a>Verze Azure Portal a CLI

Když nasadíte cluster Azure Red Hat OpenShift na portálu nebo pomocí rozhraní příkazového řádku Azure CLI, cluster se použije jako výchozí nejnovější (N) podverze a nejnovější kritická oprava. Pokud například Azure Red Hat OpenShift podporuje 4.5. z a 4.4. z, výchozí verze pro nové instalace je 4.5. z. Zákazníci, kteří chtějí použít nejnovější nadřazených verzí OCP (N + 1, N + 2), můžou cluster kdykoli upgradovat na všechny verze dostupné v stabilních kanálech pro upgrade.

## <a name="azure-red-hat-openshift-release-calendar"></a>Kalendář vydaných verzí Azure Red Hat OpenShift

Další informace najdete v následující příručce k [historii verzí v minulosti Red Hat OpenShift Container Platform (pro odesílání dat)](https://access.redhat.com/support/policy/updates/openshift/#dates).

|OCP – verze|Verze nadřazeného softwaru|Azure Red Hat OpenShift – Obecná dostupnost|Konec života|
|-|-|-|-|
|4.3|Leden 2020|Duben 2020| Srpen 2020|
|4.4|Květen 2020|Červenec 2020|4,6 GA|
|4.5|Červenec 2020| Listopad 2020|4,7 GA
|4,6|Říjen 2020| Únor 2021|4,8 GA|

## <a name="faq"></a>Časté otázky

**Co se stane, když uživatel upgraduje cluster OpenShift s menší verzí, která není podporovaná?**

Pokud používáte verzi N-2 nebo starší, znamená to, že jste mimo podporu, a zobrazí se výzva k upgradu. Po úspěšném dokončení upgradu z verze N-2 na N-1 se vrátíte do našich zásad podpory. Například:
* Pokud je nejstarší podporovaná verze Azure Red Hat OpenShift 4.4 4.4. z a jste na 4.3. z nebo starší, nejste mimo podporu.
* Po úspěšném dokončení upgradu z 4.3. z na 4.4. z nebo vyšší se vrátíte do našich zásad podpory.

Vrácení clusteru zpět na předchozí verzi, nebo vrácení zpět, se nepodporuje. Podporuje se jenom upgrade na novější verzi.

**Co znamená "mimo podporu"?**

"Mimo podporu" znamená, že verze, kterou používáte, je mimo seznam podporovaných verzí a při žádosti o podporu se může zobrazit výzva k upgradu clusteru na podporovanou verzi, pokud nejste v období odkladu po vyřazení verzí. Azure Red Hat OpenShift navíc neposkytuje žádné záruky za modul runtime ani SLA pro clustery mimo seznam podporovaných verzí na konci 30denní lhůty.
