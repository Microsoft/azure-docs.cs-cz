---
title: Koncepty služby Azure Event Grid
description: Popisuje Azure Event Grid a jeho koncepty. Definuje několika klíčovými komponentami služby Event Grid.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: tomfitz
ms.openlocfilehash: 89f0f5847f157cff59a57f7958508e4f260355c3
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50747554"
---
# <a name="concepts-in-azure-event-grid"></a>Koncepty ve službě Azure Event Grid

Tento článek popisuje hlavní koncepty ve službě Azure Event Grid.

## <a name="events"></a>Události

Událost je nejmenší velikost informace popisující plně něco, ke které došlo v systému. Každé události je běžné informace, například: Zdroj události, čas události trvalo místo a jedinečný identifikátor. Každou událost má také určité informace, které jsou pouze relevantní pro konkrétní typ události. Třeba událost o nový soubor, který vytváří ve službě Azure Storage obsahuje podrobnosti o souboru, například `lastTimeModified` hodnotu. Nebo událost Event Hubs má adresu URL zachytávací soubor. 

Každá událost je omezená na 64 KB dat.

Vlastnosti, které se odesílají v události, naleznete v tématu [schéma událostí služby Azure Event Grid](event-schema.md).

## <a name="publishers"></a>Vydavatelé

Vydavatel je uživatel nebo organizace, která určuje, k odesílání událostí do služby Event Grid. Společnost Microsoft publikuje události pro několik služeb Azure. Publikovat události z vaší aplikace. Organizace, které hostují služby mimo Azure, můžete publikovat události prostřednictvím služby Event Grid.

## <a name="event-sources"></a>Zdroje událostí

Zdroj událostí je, kde k události dojde. Každý zdroj události se vztahuje na jeden nebo více typů událostí. Zdroj události pro události vytvoření objektu blob je například Azure Storage. IoT Hub je zdroj události pro zařízení se vytvořilo události. Aplikace je zdroj události pro vlastní události, které definujete. Za odesílání událostí do služby Event Grid jsou zdroje událostí.

Informace o implementaci některý z podporovaných zdrojů služby Event Grid najdete v tématu [zdroje událostí ve službě Azure Event Grid](event-sources.md).

## <a name="topics"></a>Témata

Téma event gridu poskytuje koncový bod kam zdroji odesílá události. Vydavatel vytvoří téma event gridu a rozhodne, zda musí téma jeden nebo více než jeden zdroj událostí. Téma se používá pro kolekci souvisejících událostí. Reakce na určitých typech událostí, rozhodněte, předplatitelé témata pro přihlášení k odběru.

Systém témata jsou předdefinované témata poskytované službami Azure. Nevidíte témata systém ve vašem předplatném Azure, vydavatele vlastní témata, protože k jejich odběru. K přihlášení k odběru zadáte informace o prostředku, který chcete přijímat události z. Tak dlouho, dokud mají přístup k prostředku, můžete se přihlásit k jeho události.

Vlastní témata jsou aplikace a témata třetích stran. Při vytváření nebo přístupem k vlastní téma, zobrazí se v rámci předplatného tohoto vlastního tématu.

Při návrhu aplikace, máte flexibilitu při rozhodování o tom, kolik témata k vytvoření. Pro velká řešení vytvoříte vlastní téma pro každou kategorii související události. Zvažte například aplikaci, která zasílá události související s úpravou uživatelských účtů a zpracování objednávek. Není pravděpodobné, že obslužná rutina události chce, aby se obě kategorie události. Vytvořte dvě vlastní témata a nechat předplatit ten, který je zajímá obslužných rutin událostí. Pro malé řešení budete pravděpodobně chtít odeslat všechny události pro jedno téma. Typy událostí, který jim vyhovuje. můžete vyfiltrovat odběratelů událostí.

## <a name="event-subscriptions"></a>Odběry událostí

Předplatné říká služby Event Grid, které události tématu máte zájem přijímají. Při vytváření odběru zadáte koncový bod pro zpracování událostí. Můžete filtrovat události, které se odesílají do koncového bodu. Můžete filtrovat podle typu události nebo vzor předmět. Další informace najdete v tématu [schéma předplatného služby Event Grid](subscription-creation-schema.md).

Příklady vytváření předplatných najdete v tématu:

* [Ukázky v Azure CLI pro Event Grid](cli-samples.md)
* [Ukázky Azure Powershellu pro Event Grid](powershell-samples.md)
* [Šablony Azure Resource Manageru pro Event Grid](template-samples.md)

Informace o tom, jak aktuální event grid předplatných najdete v tématu [dotazování odběrů Event gridu](query-event-subscriptions.md).

## <a name="event-subscription-expiration"></a>Události vypršení platnosti předplatného

[Rozšíření služby Event Grid](/cli/azure/azure-cli-extensions-list) pro rozhraní příkazového řádku Azure vám umožní nastavit vypršení datum vytvoření odběru událostí. Pokud používáte rozhraní REST API, použijte `api-version=2018-09-15-preview`

Odběr událostí je automaticky vypršela po tomto datu. Nastavit dobu platnosti pro odběry událostí, které jsou vyžadovány pouze po omezenou dobu a nechcete, aby se starat o vyčištění těchto předplatných. Při vytváření odběru událostí pro scénář testování, můžete chtít nastavit vypršení. 

Příkladem vypršení nastavení, najdete v části [přihlásit k odběru s rozšířené filtry](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Obslužné rutiny událostí

Obslužná rutina události z hlediska služby Event Grid, je místo, kde událost je odeslána. Obslužná rutina provede některé další akci zpracovat událost. Event Grid podporuje několik typů obslužné rutiny. Podporované služby Azure nebo vašeho vlastního webhooku můžete použít jako obslužná rutina. V závislosti na typu obslužné rutiny se řídí různé mechanismy zaručit doručování událostí služby Event Grid. Pro obslužné rutiny události webhooku HTTP, je událost opakovat, dokud obslužná rutina vrátí stavový kód `200 – OK`. Pro fronty Azure Storage jsou události opakovat, dokud služba front úspěšně zpracuje nabízených zpráv do fronty.

Informace o implementaci některý z podporovaných obslužné rutiny služby Event Grid najdete v tématu [obslužných rutin událostí ve službě Azure Event Grid](event-handlers.md).

## <a name="security"></a>Zabezpečení

Event gridu poskytuje zabezpečení registrace k odběru témat a témat publikování. Při přihlášení k odběru, musí mít odpovídající oprávnění k danému tématu mřížky prostředku nebo události. Při publikování, musí mít SAS token nebo ověřování pomocí klíče tématu. Další informace najdete v tématu [ověřování a zabezpečení služby Event Grid](security-authentication.md).

## <a name="event-delivery"></a>Doručování událostí

Pokud služby Event Grid nelze potvrdit, že koncový bod odběratele obdržel událost, redelivers události. Další informace najdete v tématu [doručování zpráv služby Event Grid a zkuste to znovu](delivery-and-retry.md).

## <a name="batching"></a>Dávkování

Pokud používáte vlastní téma, události musí být vždy publikovány v poli. Může to být batch jednoho pro scénáře s nízkou propustností, ale pro vysoké objemy případy použití, se doporučuje batch několik událostí společně za publikování na dosáhnout vyšší efektivity. Dávky může být až 1 MB. Každé události by neměly být stále víc než 64 KB.

## <a name="next-steps"></a>Další postup

* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat služby Event Grid, přečtěte si téma [vytvoření a směrování vlastních událostí pomocí služby Azure Event Grid](custom-event-quickstart.md).
