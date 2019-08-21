---
title: Jak poskytnout zpětnou vazbu mezi daty Azure Maps | Microsoft Docs
description: Poskytnutí zpětné vazby k datům pomocí nástroje Azure Maps feedback tool.
author: walsehgal
ms.author: v-musehg
ms.date: 08/19/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 076f98cb240014bcc88a395902203413e31fe0f1
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642120"
---
# <a name="provide-data-feedback-to-azure-maps"></a>Poskytnutí zpětné vazby k datům Azure Maps

Azure Maps je všeobecně dostupná od května 2018, poskytuje nová data mapy, snadno použitelná rozhraní REST API a výkonné sady SDK pro podporu našich podnikových zákazníků v různých případech použití obchodních procesů. Reálný svět se mění každou sekundu a je zásadní pro nás, abychom zákazníkům poskytli faktickou digitální reprezentaci. Naši zákazníci, kteří plánují otevřít nebo zavřít zařízení, musí mít jistotu, že se naše mapy budou aktualizovat okamžitě, aby mohli efektivně plánovat doručování, údržbu nebo zákaznické služby na správných zařízeních. Vytvořili jsme nástroj pro zasílání zpětné vazby dat Azure Maps, abychom zákazníkům poskytli přímý názor na data. Zpětná vazba na data zákazníků směřuje přímo poskytovatelům dat a jejich editorům, kteří můžou rychle vyhodnocovat a začlenit zpětnou vazbu do našich mapovacích produktů.  

[Azure Maps Nástroj pro zpětnou vazbu dat](https://feedback.azuremaps.com) poskytuje zákazníkům snadný způsob, jak poskytnout zpětnou vazbu k datům map, zejména na obchodních místech zájmů a na domácích adresách. Tento článek vás provede postupem poskytování různých druhů zpětné vazby pomocí nástroje Azure Maps Feedback.

## <a name="add-a-business-place-or-a-residential-address"></a>Přidat obchodní místo nebo domácí adresu 

Je možné, že budete chtít poskytnout zpětnou vazbu na chybějící bod zájmu nebo na adresu domů na mapě. Existují dva způsoby, jak to provést, otevřít nástroj pro zpětnou vazbu dat mapy Azure a vyhledat souřadnice chybějícího umístění a kliknout na Přidat místo.

  ![hledání chybějícího umístění](./media/how-to-use-feedback-tool/search-poi.png)

Můžete také s mapou pracovat a kliknout na umístění a odstranit PIN kód na souřadnici a kliknout na Přidat místo. 

  ![přidat kód PIN](./media/how-to-use-feedback-tool/add-poi.png)

Po kliknutí budete přesměrováni na formulář, který poskytne příslušné údaje pro dané místo.

  ![Přidat místo](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>Oprava obchodního místa nebo domácí adresy 

Nástroj pro zpětnou vazbu vám také umožní vyhledat a najít obchodní místo nebo adresu a poskytnout zpětnou vazbu k opravě adresy nebo místa pro PIN kód, pokud nejsou správné. Chcete-li poskytnout zpětnou vazbu k opravě adresy, použijte panel hledání k vyhledání obchodního místa nebo domácí adresy. V seznamu výsledků klikněte na umístění vašeho zájmu a potom klikněte na opravit toto místo.

  ![Hledat místo pro opravu](./media/how-to-use-feedback-tool/fix-place.png)

Pokud chcete poskytnout zpětnou vazbu k opravě adresy, vyplňte formulář opravit místo a pak klikněte na tlačítko Odeslat.

  ![opravit formulář](./media/how-to-use-feedback-tool/fix-form.png)

Pokud není umístění PIN kódu pro toto místo správné, zaškrtněte políčko ve formuláři opravit místo, které říká "umístění PIN není správné" a přesuňte kód PIN do správného umístění a klikněte na tlačítko Odeslat.

  ![přesunout umístění PIN kódu](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>Přidat komentář 

Kromě toho, že vám umožní vyhledat umístění, vám Nástroj pro zpětnou vazbu umožní přidat bezplatný textový komentář pro podrobnosti týkající se umístění. Chcete-li přidat hledání komentáře pro umístění nebo klikněte na umístění a klikněte na Přidat komentář, napište komentář a klikněte na Odeslat. 

  ![Přidat komentář](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>Stav sledování 

Stav žádosti můžete sledovat také tak, že zkontrolujete pole chci sledovat stav a zadáte e-mailovou zprávu, která vám poskytne e-mail s žádostí. V e-mailu se zobrazí odkaz pro sledování, který poskytuje aktuální stav vaší žádosti. 

  ![stav zpětné vazby](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>Další postup

Pokud chcete zveřejnit jakékoli technické dotazy týkající se Azure Maps, navštivte:

* [Azure Maps Stack Overflow](https://stackoverflow.com/questions/tagged/azure-maps)
* [Fórum Azure Maps Feedback](https://feedback.azure.com/forums/909172-azure-maps)