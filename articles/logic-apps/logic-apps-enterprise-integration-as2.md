---
title: Posílání a přijímání zpráv AS2 pro B2B
description: Zprávy Exchange AS2 pro scénáře integrace B2B Enterprise pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 08/22/2019
ms.openlocfilehash: b2d7c8840da3bb44f9e220f2963dc4fee63176e2
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790715"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Zprávy Exchange AS2 pro integraci B2B Enterprise v Azure Logic Apps s využitím Enterprise Integration Pack

Pokud chcete pracovat se AS2 zprávami v Azure Logic Apps, můžete použít konektor AS2, který poskytuje triggery a akce pro správu komunikace AS2. Chcete-li například vytvořit zabezpečení a spolehlivost při odesílání zpráv, můžete použít tyto akce:

* [ **AS2 zakódovat** ](#encode) pro poskytování šifrování, digitálního podepisování a potvrzení prostřednictvím oznámení o vyřazení zpráv (MDN), které vám pomůžou zajistit Neodmítnutí. Tato akce například používá hlavičky AS2/HTTP a při konfiguraci provádí tyto úlohy:

  * Podepíše odchozí zprávy.
  * Šifruje odchozí zprávy.
  * Zkomprimuje zprávu.
  * Přenese název souboru do hlavičky MIME.

* [ **AS2 dekódovat** akci](#decode) pro poskytování dešifrování, digitálního podepisování a potvrzování prostřednictvím oznámení o zařazování zpráv (MDN). Tato akce například provede tyto úlohy:

  * Zpracuje hlavičky AS2/HTTP.
  * Sjednotí přijaté MDNs s původními odchozími zprávami.
  * Aktualizuje a koreluje záznamy v databázi bez odmítnutí.
  * Zapisuje záznamy pro vytváření sestav o stavu AS2.
  * Vytvoří výstup obsahu v datové části jako kódovaný v kódování Base64.
  * Určuje, zda jsou vyžadovány MDNs. Na základě smlouvy AS2 určuje, jestli má být MDNs synchronní nebo asynchronní.
  * Generuje synchronní nebo asynchronní MDNs na základě smlouvy AS2.
  * Nastaví korelační tokeny a vlastnosti v MDNs.

  Tato akce provádí tyto úlohy také při konfiguraci:

  * Ověří podpis.
  * Dešifruje zprávy.
  * Dekomprimuje zprávu.
  * Zkontroluje a zakáže duplicity ID zpráv.

Tento článek ukazuje, jak přidat akce kódování a dekódování AS2 do existující aplikace logiky.

> [!IMPORTANT]
> Původní konektor AS2 bude zastaralý, takže se místo toho použije konektor **AS2 (v2)** . Tato verze poskytuje stejné možnosti jako původní verze, je nativní pro modul runtime Logic Apps a přináší významná vylepšení výkonu z hlediska propustnosti a velikosti zpráv. Nativní konektor v2 také nevyžaduje, abyste vytvořili připojení k účtu pro integraci. Místo toho, jak je popsáno v části požadavky, nezapomeňte propojit účet pro integraci do aplikace logiky, ve které plánujete konektor používat.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, ze které chcete použít konektor AS2, a Trigger, který spouští pracovní postup vaší aplikace logiky. Konektor AS2 poskytuje pouze akce, nikoli triggery. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , který je přidružený k vašemu předplatnému Azure a propojený s aplikací logiky, ve které plánujete použít konektor AS2. Vaše aplikace logiky i účet pro integraci musí existovat ve stejném umístění nebo oblasti Azure.

* Aspoň dva [obchodní partneři](../logic-apps/logic-apps-enterprise-integration-partners.md) , které jste už definovali v účtu pro integraci pomocí kvalifikátoru identity AS2.

* Než budete moct použít konektor AS2, musíte vytvořit AS2 [smlouvu](../logic-apps/logic-apps-enterprise-integration-agreements.md) mezi vašimi obchodními partnery a uložit tuto smlouvu na účet pro integraci.

* Pokud používáte [Azure Key Vault](../key-vault/key-vault-overview.md) pro správu certifikátů, ověřte, že klíče trezoru umožňují operace **šifrování** a **dešifrování** . V opačném případě se akce kódování a dekódování nezdaří.

  V Azure Portal přejdete do svého trezoru klíčů, zobrazíte **povolené operace**klíče trezoru a ověříte, že jsou vybrané operace **šifrování** a **dešifrování** .

  ![Kontrolovat operace klíče trezoru](media/logic-apps-enterprise-integration-as2/vault-key-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Kódování zpráv AS2

1. Pokud jste to ještě neudělali, v [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V Návrháři přidejte novou akci do aplikace logiky.

1. V části **Zvolte akci** a vyhledávací pole vyberte **vše**. Do vyhledávacího pole zadejte "AS2 encode" a ujistěte se, že jste vybrali akci AS2 (v2): **kódování AS2**

   ![Vyberte AS2 kódování.](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Nyní zadejte informace pro tyto vlastnosti:

   | Vlastnost | Popis |
   |----------|-------------|
   | **Zpráva ke kódování** | Datová část zprávy |
   | **AS2 od** | Identifikátor odesilatele zprávy, jak Určuje smlouva AS2 |
   | **AS2 na** | Identifikátor příjemce zprávy, jak Určuje smlouva AS2 |
   |||

   Například:

   ![Vlastnosti kódování zprávy](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Dekódovat zprávy AS2

1. Pokud jste to ještě neudělali, v [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V Návrháři přidejte novou akci do aplikace logiky.

1. V části **Zvolte akci** a vyhledávací pole vyberte **vše**. Do vyhledávacího pole zadejte "AS2 Decode" a ujistěte se, že jste vybrali akci AS2 (v2): **AS2 Decode**

   ![Vyberte AS2 dekódování.](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Pro **zprávu pro zakódování** a vlastnosti **záhlaví zprávy** vyberte tyto hodnoty z předchozích výstupů triggeru nebo akce.

   Předpokládejme například, že vaše aplikace logiky přijímá zprávy prostřednictvím triggeru žádosti. Z této aktivační události můžete vybrat výstupy.

   ![Výběr textu a záhlaví z výstupů žádosti](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>Ukázka

Pokud se chcete pokusit nasadit plně funkční aplikaci logiky a ukázkový scénář AS2, přečtěte si článek [Šablona a scénář AS2 Logic App](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Referenční informace ke konektorům

Podrobnosti o technických podrobnostech, jako jsou triggery, akce a omezení, jak je popsáno v souboru OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce konektoru](/connectors/as2/).

## <a name="next-steps"></a>Další kroky

Další informace o [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
