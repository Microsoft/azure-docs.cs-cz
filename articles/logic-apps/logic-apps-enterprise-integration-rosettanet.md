---
title: Zprávy RosettaNet pro integraci B2B
description: Zprávy Exchange RosettaNet v aplikacích Azure Logic Apps s podnikovou integrací Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 2cc2ac08b9624c1d1d9bee9ce91a7c91189d7f2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792415"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Zprávy Exchange RosettaNet pro podnikovou integraci B2B v Aplikacích Azure Logic Apps

[RosettaNet](https://resources.gs1us.org) je neziskové konsorcium, které zavedlo standardní procesy pro sdílení obchodních informací. Tyto normy se běžně používají pro procesy dodavatelského řetězce a jsou rozšířené v polovodičovém, elektronickém a logistickém průmyslu. Konsorcium RosettaNet vytváří a udržuje procesy rozhraní partnerů (PIPs), které poskytují společné definice obchodních procesů pro všechny výměny zpráv RosettaNet. RosettaNet je založen na XML a definuje pokyny pro zprávy, rozhraní pro obchodní procesy a implementační rámce pro komunikaci mezi společnostmi.

V [Aplikacích Azure Logic Apps](../logic-apps/logic-apps-overview.md)vám konektor RosettaNet pomůže vytvářet integrační řešení, která podporují standardy RosettaNet. Konektor je založen na RosettaNet Implementace Framework (RNIF) verze 2.0.01. RNIF je otevřený síťový aplikační rámec, který umožňuje obchodním partnerům spolupracovat s RosettaNet PIPs. Tento rámec definuje strukturu zpráv, potřebu potvrzení, víceúčelové kódování rozšíření Internet Mail Extensions (MIME) a digitální podpis.

Konkrétně konektor poskytuje tyto možnosti:

* Zakódovat nebo přijímat zprávy RosettaNet.
* Dekódovat nebo odesílat zprávy RosettaNet.
* Počkejte na odpověď a generování oznámení o selhání.

Pro tyto funkce konektor podporuje všechny PIPs, které jsou definovány RNIF 2.0.01. Komunikace s partnerem může být synchronní nebo asynchronní.

## <a name="rosettanet-concepts"></a>Koncepty RosettaNet

Zde jsou některé koncepty a termíny, které jsou jedinečné pro specifikaci RosettaNet a jsou důležité při vytváření integrace založených na RosettaNet:

* **Pip**

  Organizace RosettaNet vytváří a udržuje procesy rozhraní partnera (PIPs), které poskytují společné definice obchodních procesů pro všechny výměny zpráv RosettaNet. Každá specifikace PIP poskytuje soubor definice typu dokumentu (DTD) a dokument s pokyny pro zprávy. Soubor DTD definuje strukturu zpráv obsahu služby. Dokument s pokyny pro zprávy, který je souborHTML čitelný pro člověka, určuje omezení na úrovni prvku. Společně tyto soubory poskytují úplnou definici obchodního procesu.

   PIPs jsou rozděleny do kategorií podle vysoké úrovně obchodní funkce nebo clusteru a dílčí funkce nebo segmentu. Například "3A4" je PIP pro nákupní objednávku, zatímco "3" je funkce Správa objednávek a "3A" je dílčí funkce Nabídka & položka objednávky. Další informace naleznete na [webu RosettaNet](https://resources.gs1us.org).

* **Akce**

  Součástí PIP, zprávy akce jsou obchodní zprávy, které jsou vyměňovány mezi partnery.

* **Signál**

   Součástí PIP, signální zprávy jsou potvrzení, které jsou odesílány v reakci na zprávy akce.

* **Jedna akce a dvojitá akce**

  Pro pip s jednou akcí je jedinou odpovědí zpráva o potvrzení signálu. U pipu s dvojitou akcí iniciátor obdrží zprávu s odpovědí a kromě toku zpráv s jednou akcí odpoví potvrzením.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účet integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) pro ukládání vaší smlouvy a další artefakty B2B. Tento účet integrace musí být přidružený k vašemu předplatnému Azure.

* Nejméně dva [partneři,](../logic-apps/logic-apps-enterprise-integration-partners.md) kteří jsou definováni ve vašem účtu integrace a konfigurováni s kvalifikátorem "DUNS" v části **Obchodní identity**

* Konfigurace procesu PIP, která je vyžadována pro odesílání nebo přijímání zpráv RosettaNet ve vašem integračním účtu. Konfigurace procesu ukládá všechny charakteristiky konfigurace PIP. Na tuto konfiguraci pak můžete odkazovat při vytváření smlouvy s partnerem. Pokud chcete vytvořit konfiguraci procesu PIP ve vašem účtu integrace, přečtěte si informace [o přidání konfigurace procesu PIP](#add-pip).

* Volitelné [certifikáty](../logic-apps/logic-apps-enterprise-integration-certificates.md) pro šifrování, dešifrování nebo podepisování zpráv, které nahrajete do účtu integrace. Certifikáty jsou vyžadovány pouze v případě, že používáte podepisování nebo šifrování.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>Přidat konfiguraci procesu PIP

Chcete-li přidat konfiguraci procesu PIP do účtu integrace, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com)najděte a otevřete svůj účet integrace.

1. V podokně **Přehled** vyberte dlaždici **RosettaNet PIP.**

   ![Zvolte dlaždici RosettaNet](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. V **části RosettaNet PIP**zvolte **Přidat**. Zadejte podrobnosti o pipu.

   ![Přidat rosettanetové podrobnosti PIP](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název** | Ano | Váš název PIP |
   | **Kód PIP** | Ano | Pip třímístný kód. Další informace naleznete v tématu [RosettaNet PIPs](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **Verze PIP** | Ano | Číslo verze PIP, které je k dispozici na základě vybraného kódu PIP |
   ||||

   Další informace o těchto vlastnostech PIP naleznete na [webových stránkách RosettaNet](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg).

1. Po dokončení zvolte **OK**, který vytvoří konfiguraci PIP.

1. Chcete-li zobrazit nebo upravit konfiguraci procesu, vyberte pip a zvolte **Upravit jako JSON**.

   Všechna nastavení konfigurace procesu pocházejí ze specifikací PIP. Aplikace logiky naplní většinu nastavení s výchozí hodnoty, které jsou nejčastěji používané hodnoty pro tyto vlastnosti.

   ![Upravit konfiguraci PIP RosettaNet](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Zkontrolujte, zda nastavení odpovídá hodnotám v příslušné specifikaci PIP a splňuje vaše obchodní potřeby. V případě potřeby aktualizujte hodnoty v JSON a uložte tyto změny.

## <a name="create-rosettanet-agreement"></a>Vytvořit smlouvu RosettaNet

1. Na [webu Azure Portal](https://portal.azure.com)najděte a otevřete svůj účet integrace, pokud už není otevřený.

1. V podokně **Přehled** vyberte dlaždici **Dohody.**

   ![Zvolte dlaždice Smlouvy.](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. V části **Smlouvy**zvolte **Přidat**. Uveďte podrobnosti o své smlouvě.

   ![Přidání podrobností o smlouvě](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název** | Ano | Název dohody |
   | **Typ smlouvy** | Ano | Vyberte **RosettaNet**. |
   | **Hostitelský partner** | Ano | Dohoda vyžaduje hostitele i partnera hosta. Hostitelský partner představuje organizaci, která konfiguruje smlouvu. |
   | **Identita hostitele** | Ano | Identifikátor hostitelského partnera |
   | **Partner pro hosty** | Ano | Dohoda vyžaduje hostitele i partnera hosta. Partner hosta představuje organizaci, která obchoduje s partnerem hostitele. |
   | **Identita hosta** | Ano | Identifikátor pro partnera hosta |
   | **Přijímat nastavení** | Různé | Tyto vlastnosti platí pro všechny zprávy přijaté hostitelským partnerem. |
   | **Odeslat nastavení** | Různé | Tyto vlastnosti platí pro všechny zprávy odeslané hostitelským partnerem. |  
   | **RosettaNet PIP odkazy** | Ano | Pip odkazy pro dohodu. Všechny zprávy RosettaNet vyžadují konfiguraci PIP. |
   ||||

1. Chcete-li nastavit smlouvu pro příjem příchozích zpráv od partnera hosta, vyberte **možnost Nastavení příjmu**.

   ![Nastavení příjmu](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Chcete-li povolit podepisování nebo šifrování příchozích zpráv, vyberte v části **Zprávy**možnost **Zpráva by měla být podepsána** nebo **zpráva by měla být šifrována.**

      | Vlastnost | Požaduje se | Popis |
      |----------|----------|-------------|
      | **Zpráva by měla být podepsána.** | Ne | Podepisovat příchozí zprávy pomocí vybraného certifikátu. |
      | **Certifikát** | Ano, pokud je povoleno podepisování | Certifikát, který se má použít k podepisování |
      | **Povolení šifrování zpráv** | Ne | Šifrování příchozích zpráv vybraným certifikátem. |
      | **Certifikát** | Ano, pokud je šifrování povoleno | Certifikát, který se má použít pro šifrování |
      ||||

   1. V rámci každého výběru vyberte příslušný [certifikát](./logic-apps-enterprise-integration-certificates.md), který jste dříve přidali do účtu integrace, který chcete použít pro podepisování nebo šifrování.

1. Chcete-li nastavit smlouvu o odesílání zpráv partnerovi hosta, vyberte **možnost Odeslat nastavení**.

   ![Nastavení odesílání](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Pokud chcete povolit podepisování nebo šifrování odchozích zpráv, vyberte v části **Zprávy**možnost **Povolit podepisování zpráv** nebo **Povolit šifrování zpráv.** V rámci každého výběru vyberte příslušný algoritmus a [certifikát](./logic-apps-enterprise-integration-certificates.md), který jste dříve přidali do účtu integrace, který chcete použít pro podepisování nebo šifrování.

      | Vlastnost | Požaduje se | Popis |
      |----------|----------|-------------|
      | **Povolení podepisování zpráv** | Ne | Odhlásit odchozí zprávy pomocí vybraného podpisového algoritmu a certifikátu. |
      | **Algoritmus podepisování** | Ano, pokud je povoleno podepisování | Podpisový algoritmus, který se má použít na základě vybraného certifikátu |
      | **Certifikát** | Ano, pokud je povoleno podepisování | Certifikát, který se má použít k podepisování |
      | **Povolení šifrování zpráv** | Ne | Šifrování odchozích dat pomocí vybraného šifrovacího algoritmu a certifikátu. |
      | **Šifrovací algoritmus** | Ano, pokud je šifrování povoleno | Šifrovací algoritmus, který se má použít na základě vybraného certifikátu |
      | **Certifikát** | Ano, pokud je šifrování povoleno | Certifikát, který se má použít pro šifrování |
      ||||

   1. V **části Koncové body**zadejte požadované adresy URL, které se mají použít pro odesílání zpráv akcí a potvrzení.

      | Vlastnost | Požaduje se | Popis |
      |----------|----------|-------------|
      | **Adresa URL akce** |  Ano | Adresa URL, která se má použít pro odesílání zpráv akcí. Adresa URL je povinné pole pro synchronní i asynchronní zprávy. |
      | **Adresa URL potvrzení** | Ano | Adresa URL, která se má použít pro odesílání potvrzení zpráv. Adresa URL je povinné pole pro asynchronní zprávy. |
      ||||

1. Chcete-li nastavit smlouvu s odkazy RosettaNet PIP pro partnery, vyberte **reference RosettaNet PIP**. V části **Název PIP**vyberte název dříve vytvořeného pipu.

   ![Odkazy pip](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   Výběr vyplní zbývající vlastnosti, které jsou založeny na pip, který nastavíte v účtu integrace. V případě potřeby můžete roli **PIP**změnit .

   ![Vybraný PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

Po dokončení těchto kroků jste připraveni odesílat nebo přijímat zprávy RosettaNet.

## <a name="rosettanet-templates"></a>Šablony RosettaNet

Chcete-li urychlit vývoj a doporučit vzory integrace, můžete použít šablony aplikace logiky pro dekódování a kódování zpráv RosettaNet. Když vytvoříte aplikaci logiky, můžete vybrat z galerie šablon v Návrháři aplikací logiky. Tyto šablony najdete také v [úložišti GitHub pro Azure Logic Apps](https://github.com/Azure/logicapps).

![Šablony RosettaNet](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>Příjem nebo dekódování zpráv RosettaNet

1. [Vytvořte prázdnou aplikaci logiky](quickstart-create-first-logic-app-workflow.md).

1. [Propojte svůj integrační účet](logic-apps-enterprise-integration-create-integration-account.md#link-account) s aplikací logiky.

1. Před přidáním akce dekódovat zprávu RosettaNet, musíte přidat aktivační událost pro spuštění aplikace logiky, jako je například request trigger.

1. Po přidání aktivační události zvolte **Nový krok**.

   ![Přidat aktivační událost požadavku](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Do vyhledávacího pole zadejte "rosettanet" a vyberte tuto akci: **RosettaNet Decode**

   ![Najít a vybrat akci "RosettaNet Decode"](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Zadejte informace o vlastnostech akce:

   ![Poskytnout podrobnosti akce](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Zprávu** | Ano | Zpráva RosettaNet k dekódování  |
   | **Hlavičky** | Ano | Hlavičky HTTP, které poskytují hodnoty pro verzi, což je verze RNIF, a typ odpovědi, který označuje typ komunikace mezi partnery a může být synchronní nebo asynchronní |
   | **Role** | Ano | Role hostitelského partnera v PIP |
   ||||

   Z akce Dekódování RosettaNet výstup spolu s dalšími vlastnostmi zahrnuje **odchozí signál**, který můžete zakódovat a vrátit zpět partnerovi nebo provést jakoukoli jinou akci na tomto výstupu.

## <a name="send-or-encode-rosettanet-messages"></a>Odesílání nebo kódování zpráv RosettaNet

1. [Vytvořte prázdnou aplikaci logiky](quickstart-create-first-logic-app-workflow.md).

1. [Propojte svůj integrační účet](logic-apps-enterprise-integration-create-integration-account.md#link-account) s aplikací logiky.

1. Před přidáním akce pro kódování zprávy RosettaNet, musíte přidat aktivační událost pro spuštění aplikace logiky, jako je například požadavek aktivační událost.

1. Po přidání aktivační události zvolte **Nový krok**.

   ![Přidat aktivační událost požadavku](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Do vyhledávacího pole zadejte "rosettanet" a vyberte tuto akci: **RosettaNet Encode**

   ![Najít a vybrat akci "RosettaNet Encode"](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Zadejte informace o vlastnostech akce:

   ![Poskytnout podrobnosti akce](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Zprávu** | Ano | Zpráva RosettaNet pro kódování  |
   | **Hostitelský partner** | Ano | Název hostitelského partnera |
   | **Partner pro hosty** | Ano | Jméno partnera hosta |
   | **Kód PIP** | Ano | Kód PIP |
   | **Verze PIP** | Ano | Verze PIP |  
   | **Identita instance PIP** | Ano | Jedinečný identifikátor této zprávy PIP |  
   | **Typ zprávy** | Ano | Typ zprávy, kterou chcete zakódovat |  
   | **Role** | Ano | Úloha hostitelského partnera |
   ||||

   Kódovaná zpráva je nyní připravena k odeslání partnerovi.

1. Chcete-li odeslat kódovku, tento příklad používá akci **HTTP,** která je přejmenována na "HTTP - Odeslat kódovnou zprávu partnerovi".

   ![Akce HTTP pro odeslání zprávy RosettaNet](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   Podle standardů RosettaNet jsou obchodní transakce považovány za úplné pouze v případě, že jsou dokončeny všechny kroky definované pipem.

1. Poté, co hostitel odešle kódovnou zprávu partnerovi, hostitel čeká na signál a potvrzení. Chcete-li provést tento úkol, přidejte **RosettaNet čekat na akci odpovědi.**

   ![Přidat akci "RosettaNet wait for response"](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   Doba čekání a počet opakování jsou založeny na konfiguraci PIP v účtu integrace. Pokud odpověď není přijata, tato akce generuje oznámení o selhání. Chcete-li zpracovat opakování, vždy dát **encode** a **čekat na odpovědi** akce do **Do** smyčky.

   ![Dokud se smyčka s akcemi RosettaNet](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak ověřit, transformovat a další operace se zprávami pomocí sady [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
