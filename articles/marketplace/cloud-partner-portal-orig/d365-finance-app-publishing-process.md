---
title: Postup publikování aplikace | Dokumentace Microsoftu
description: .
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 0fc82229e158ed35b97203b11d08841c683c45ce
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809324"
---
<a name="app-publishing-steps"></a>Postup publikování aplikace
====================

Chcete-li zahájit proces publikování, bude "Publikovat" klikněte v části kartu Editor.

![Tlačítko publikovat aplikaci CPP](./media/d365-financials/image014.jpg)


Na kartě Stav se zobrazí publikování kroky určující, kde se nachází vaši nabídku v procesu publikování. Kdykoli během procesu publikování můžete přihlásit a klikněte na kartu všechny nabízí zobrazíte nejnovější stav pro všechny svoje nabídky. Můžete kliknout přímo na stav vaší nabídky a podrobnosti o tom, kde se nachází vaši nabídku v procesu publikování.

Projděme si všechny kroky pro publikování, popisují, co se stane v každém kroku a jak dlouho by měl odhadnou každý krok bude trvat.

![Publikování diagramu procesu](./media/d365-financials/image017.png)


**Ověřit požadované součásti**

Když kliknete na možnost "Publikovat", bude trvat automatickou kontrolu určitým postupem zajistit, že máte na vaši nabídku naplněné všechna povinná pole. Pokud nejsou všechna pole vyplněné, upozornění se zobrazí vedle pole a budete muset naplnit přesně pak klikněte na možnost "Publikovat" znovu.

Jakmile dokončíte tento krok správně, se zobrazí automaticky otevírané okno s dotazem e-mailových adres, který se použije k zasílání oznámení publikování. Jakmile odešlete e-mailovou adresu, tento krok je dokončena.


**Ověřování automatizované aplikací**

V tomto kroku našich automatizovaných certifikační služby ověří, zda rozšíření aplikací, které jsou součástí nabídky, který svůj obsah v souladu s nabízejí metadat. Ujistěte se, že název aplikace, verze, vydavatel a ID odpovídá zadané v manifestu rozšíření s názvem vždy `app.json`.


**Ověření testů jednotky**

Pokud jste se rozhodli pro nastavení testovací verze, je této fáze, ve kterém se ověří vaše nastavení testu jednotky.


**Ověření průběžné správy a registrace**

Během této fáze Pokud jste nakonfigurovali funkci generování vést jsme se ověření funkčnosti integraci vašich CRM odesláním testu nový zájemce do CRM. Zobrazí se záznam pomocí falešných dat naplnit v CRM nebo Azure Table po dokončení tohoto kroku. Veškerou dokumentaci pro generování vést je umístěná tady.


**Balení AppSource**

Podrobnosti o artefaktů z prodejních míst jsou kontrolovány a AppSource náhled balíčku je právě generován.


**Vydavatel Odhlásit se.**

Během této fáze **aktivace** tlačítko je teď aktivní. Také teď bude mít odkaz na vaši nabídku (s hidekey) ve verzi preview. Jakmile budete spokojeni s vzhled ve verzi preview, klikněte na tlačítko Přejít Live.
Mějte na paměti, tento požadavek se nepoužívá publikujte svoje aplikace ve zdroji aplikace, ale místo toho spustí proces Naše interní ověřování.


**Marketingové a technické aplikace ověřování**

Tento krok je, kde provádíme marketingové a technické ověření paralelně. Odkazovat [kontrolní seznam pro vaše aplikace odesílá](https://aka.ms/CheckBeforeYouSubmit) a [vývoj aplikací pro Dynamics 365 pro Finance and Operations dokument white paper](https://go.microsoft.com/fwlink/?linkid=841518) doprovodné materiály dokumentace pro povinné požadavky a doporučení. Během procesu ověření provedeme následující:
-  spolupracovat s vámi na všechny nevyřízené otázky a problémy.  
- můžete zadat datum publikování aplikace a upozorní vás při publikování aplikace. 
- nabízí první zpětnou vazbu týkající se technickou podporu a marketing ověření během 5 – 7 pracovních dnů.

Tyto kroky může obvykle trvat za týden a není nutné pro vám nepřetržitě protokolu na portál partnerů cloudu.


**Publikování aplikace ve službě**

Vaše nabídka právě probíhá konečné zpracování. Aplikace byla úspěšně ověřena marketingové a technické, ale teď musí projít konečné zpracování připravit pro aplikace zdroj.


**Živé**

Vaše nabídka je teď živě na AppSource a zákazníci budou moci zobrazit a nasazení vaší aplikace ve službě Microsoft Dynamics 365 Business Central předplatná. Dostanete e-mail od nás upozorněním, že vaše aplikace byl zveřejněn ve zdroji aplikace. V libovolném okamžiku můžete kliknout na kartě vše nabízí a zobrazit stav pro všechny vaše nabídky, které jsou uvedené v pravém sloupci. Kliknutím na stav zobrazíte stav publikování tok v souvislosti s vaší nabídky.


<a name="error-handling"></a>Zpracování chyb
--------------

Během procesu publikování může došlo k chybě. Pokud dojde k chybě, zobrazí se oznamovací e-mail oznamující, že došlo k chybě. pokyny k dalším krokům. Můžete také zobrazit chyby kdykoli během tohoto procesu kliknutím karty stav. Zobrazí se, které bod v procesu, který spolu s chybovou zprávu sbalování, co je potřeba vyřešit došlo k chybě.

Pokud narazíte na chyby během procesu publikování, je nutné provést tyto chyby opravte a potom klikněte na **publikovat** celý proces. Je třeba spustit na začátku postupu publikování v **ověřit požadavky** při opětovné po všechny opravy chyb.

Pokud máte problémy řeší chybu, měli byste otevřít žádost o podporu jak získat pomoc od našimi techniky podpory.


<a name="canceling-the-publishing-request"></a>Zrušení publikování požadavku
--------------------------------

Můžete zahájit proces publikování a je potřeba zrušit vaši žádost. Zrušit žádosti o publikování můžete pouze po publikování požadavek dosáhne krok schvalování vydavatele. Pokud chcete zrušit, klikněte na **zrušit publikování**. Stav publikování se resetuje ke kroku 1 a znovu publikovat, by měl klikněte na publikovat a postupujte podle stavu.

![Zrušit publikování tlačítko](./media/d365-financials/image013.png)
