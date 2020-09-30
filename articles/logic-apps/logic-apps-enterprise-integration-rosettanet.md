---
title: Zprávy RosettaNet pro integraci B2B
description: Výměna zpráv RosettaNet v Azure Logic Apps s Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: f02cbdc7ca8822c5fcc91b106856d7f8f547536b
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91565101"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Zprávy Exchange RosettaNet pro integraci B2B Enterprise v Azure Logic Apps

[RosettaNet](https://resources.gs1us.org) je nezisková konsorcium, která zřídila standardní procesy pro sdílení firemních informací. Tyto standardy se běžně používají pro procesy dodavatelských řetězců a jsou rozšířeny do polovodičového, elektronikého a logistického průmyslu. RosettaNet Consortium vytvoří a zachovává procesy partnerských rozhraní (PIPs), které poskytují běžné definice obchodních procesů pro všechny výměny zpráv RosettaNet. RosettaNet je založen na XML a definuje pokyny pro zprávy, rozhraní pro obchodní procesy a implementační architektury pro komunikaci mezi společnostmi.

Konektor RosettaNet vám v [Azure Logic Apps](../logic-apps/logic-apps-overview.md)pomůže vytvořit integrační řešení, která podporují standardy RosettaNet. Konektor je založen na RosettaNet Implementation Framework (RNIF) verze 2.0.01. RNIF je otevřená síťová aplikační architektura, která umožňuje obchodním partnerům spolupracovat s RosettaNet PIPs. Toto rozhraní definuje strukturu zpráv, nutnost potvrzení, kódování MIME (Multipurpose Internet Mail Extensions) a digitální podpis.

Konkrétně konektor poskytuje tyto možnosti:

* Kódování nebo příjem zpráv RosettaNet
* Dekódování nebo posílání zpráv RosettaNet
* Počkejte na odpověď a generování oznámení o selhání.

Pro tyto funkce konektor podporuje všechny PIPs, které jsou definovány pomocí RNIF 2.0.01. Komunikace s partnerem může být synchronní nebo asynchronní.

## <a name="rosettanet-concepts"></a>Koncepty RosettaNet

Tady je několik konceptů a pojmů, které jsou jedinečné pro specifikaci RosettaNet a jsou důležité při vytváření integrace založené na RosettaNet:

* **PIP**

  RosettaNet organizace vytváří a udržuje procesy partnerských rozhraní (PIPs), které poskytují běžné definice obchodních procesů pro všechny výměny zpráv RosettaNet. Každá specifikace PIP poskytuje soubor definice typu dokumentu (DTD) a dokument s pokyny pro zprávu. Soubor DTD definuje strukturu zpráv obsahu služby. Dokument s pokyny pro zprávy, který je uživatelsky čitelný soubor HTML, určuje omezení na úrovni elementu. Společně tyto soubory poskytují úplnou definici obchodního procesu.

   PIPs jsou zařazené do kategorií podle funkce nebo clusteru vysoké úrovně, a podfunkce nebo segmentu. Například "3A4" je PIP pro nákupní objednávku, zatímco "3" je funkce pro správu objednávek a "3A" je nabídka pro zadání objednávky & objednávka. Další informace najdete na [webu RosettaNet](https://resources.gs1us.org).

* **Akce**

  Součástí PIP jsou zprávy o akcích, které se vyměňují mezi partnery.

* **Nyní**

   Součástí PIP jsou potvrzovací zprávy, které se odesílají jako odpověď na zprávy akce.

* **Jedna akce a Dvojitá akce**

  V případě PIP s jednou akcí je jedinou odpovědí zpráva potvrzujícího signálu. V případě, že se jedná o akci PIP s dvojitou odezvou, iniciátor obdrží zprávu odpovědi a kromě toku zpráv s jednou akcí odpoví spolu s potvrzením.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) pro ukládání vaší smlouvy a dalších artefaktů B2B. Tento účet integrace musí být přidružený k vašemu předplatnému Azure.

* Aspoň dva [partneři](../logic-apps/logic-apps-enterprise-integration-partners.md) , kteří jsou definováni v účtu integrace a nakonfigurovali s kvalifikátorem "Duns" v rámci **obchodních identit**

* Konfigurace procesu PIP, která je nutná k posílání nebo přijímání zpráv RosettaNet, ve vašem účtu integrace. Konfigurace procesu ukládá všechny charakteristiky konfigurace PIP. Tuto konfiguraci pak můžete odkázat při vytváření smlouvy s partnerem. Pokud chcete vytvořit konfiguraci procesu PIP v účtu integrace, přečtěte si téma [Přidání konfigurace procesu PIP](#add-pip).

* Volitelné [certifikáty](../logic-apps/logic-apps-enterprise-integration-certificates.md) pro šifrování, dešifrování nebo podepsání zpráv, které nahráváte do účtu pro integraci. Certifikáty se vyžadují jenom v případě, že používáte podepisování nebo šifrování.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>Přidat konfiguraci procesu PIP

Pokud chcete přidat konfiguraci procesu PIP do účtu pro integraci, postupujte takto:

1. V [Azure Portal](https://portal.azure.com)vyhledejte a otevřete účet pro integraci.

1. V podokně **Přehled** vyberte dlaždici **RosettaNet PIP** .

   ![Zvolit dlaždici RosettaNet](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. V části **ROSETTANET PIP**vyberte **Přidat**. Zadejte podrobnosti PIP.

   ![Přidat podrobnosti PIP RosettaNet](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název** | Ano | Název PIP |
   | **Kód PIP** | Ano | Kód pro tři číslice v PIP. Další informace najdete v tématu [RosettaNet Pips](/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **Verze PIP** | Ano | Číslo verze PIP, které je k dispozici na základě vybraného kódu PIP |
   ||||

   Další informace o těchto vlastnostech PIP najdete na [webu RosettaNet](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg).

1. Až budete hotovi, klikněte na **tlačítko OK**, čímž se vytvoří konfigurace PIP.

1. Chcete-li zobrazit nebo upravit konfiguraci procesu, vyberte PIP a zvolte **Upravit jako JSON**.

   Všechna nastavení konfigurace procesu pocházejí ze specifikací PIP. Logic Apps naplní většinu nastavení výchozími hodnotami, které jsou nejčastěji používanými hodnotami pro tyto vlastnosti.

   ![Upravit konfiguraci RosettaNet PIP](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Potvrďte, že nastavení odpovídají hodnotám ve vhodné specifikaci PIP a vyhovují vašim obchodním potřebám. V případě potřeby aktualizujte hodnoty ve formátu JSON a uložte tyto změny.

## <a name="create-rosettanet-agreement"></a>Vytvoření smlouvy RosettaNet

1. V [Azure Portal](https://portal.azure.com)vyhledejte a otevřete účet pro integraci, pokud ještě není otevřený.

1. V podokně **Přehled** vyberte dlaždici **smlouvy** .

   ![Vybrat dlaždici smlouvy](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. V části **smlouvy**klikněte na možnost **Přidat**. Zadejte podrobnosti smlouvy.

   ![Přidat podrobnosti smlouvy](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název** | Ano | Název smlouvy |
   | **Typ smlouvy** | Ano | Vyberte **RosettaNet**. |
   | **Partner hostitele** | Ano | Smlouva vyžaduje hostitele i partnera hosta. Partner hostitele představuje organizaci, která konfiguruje smlouvu. |
   | **Hostitelská identita** | Ano | Identifikátor hostitelského partnera |
   | **Partner hosta** | Ano | Smlouva vyžaduje hostitele i partnera hosta. Partner hosta představuje organizaci, která provádí podnikání s hostitelským partnerem. |
   | **Identita hosta** | Ano | Identifikátor hostovaného partnera |
   | **Nastavení příjmu** | Různé | Tyto vlastnosti se vztahují na všechny zprávy přijaté hostitelským partnerem. |
   | **Nastavení odesílání** | Různé | Tyto vlastnosti se vztahují na všechny zprávy odesílané hostitelským partnerem. |  
   | **Odkazy na RosettaNet PIP** | Ano | Odkazy na PIP smlouvy. Všechny zprávy RosettaNet vyžadují konfigurace PIP. |
   ||||

1. Pokud chcete nastavit smlouvu pro příjem příchozích zpráv od partnera hosta, vyberte **přijmout nastavení**.

   ![Nastavení příjmu](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Chcete-li povolit podepisování nebo šifrování pro příchozí zprávy, vyberte v části **zprávy**možnost **zpráva by měla být podepsána** nebo **zpráva by měla být zašifrována** .

      | Vlastnost | Požaduje se | Popis |
      |----------|----------|-------------|
      | **Zpráva by měla být podepsaná.** | Ne | Podepsat příchozí zprávy s vybraným certifikátem. |
      | **Certifikát** | Ano, pokud je povoleno podepisování | Certifikát, který se má použít pro podepisování |
      | **Povolit šifrování zpráv** | Ne | Zašifruje příchozí zprávy s vybraným certifikátem. |
      | **Certifikát** | Ano, pokud je povoleno šifrování | Certifikát, který se má použít pro šifrování |
      ||||

   1. Pod každým výběrem vyberte příslušný [certifikát](./logic-apps-enterprise-integration-certificates.md), který jste dříve přidali do účtu pro integraci, abyste ho mohli použít k podepisování nebo šifrování.

1. Pokud chcete nastavit smlouvu o posílání zpráv partnerovi hosta, vyberte **Odeslat nastavení**.

   ![Nastavení odesílání](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Chcete-li povolit podepisování nebo šifrování odchozích zpráv, v části **zprávy**vyberte možnost **povolit podepisování zpráv** nebo **Povolit šifrování zpráv** v uvedeném pořadí. Pod každým výběrem vyberte příslušný algoritmus a [certifikát](./logic-apps-enterprise-integration-certificates.md), který jste dříve přidali do svého účtu pro integraci, abyste se mohli použít k podepisování nebo šifrování.

      | Vlastnost | Požaduje se | Popis |
      |----------|----------|-------------|
      | **Povolit podepisování zpráv** | Ne | Podepisujte odchozí zprávy s vybraným podpisovým algoritmem a certifikátem. |
      | **Podpisový algoritmus** | Ano, pokud je povoleno podepisování | Podpisový algoritmus, který se má použít, na základě vybraného certifikátu |
      | **Certifikát** | Ano, pokud je povoleno podepisování | Certifikát, který se má použít pro podepisování |
      | **Povolit šifrování zpráv** | Ne | Zašifrujte odchozí zprávy s vybraným šifrovacím algoritmem a certifikátem. |
      | **Šifrovací algoritmus** | Ano, pokud je povoleno šifrování | Šifrovací algoritmus, který se má použít, na základě vybraného certifikátu |
      | **Certifikát** | Ano, pokud je povoleno šifrování | Certifikát, který se má použít pro šifrování |
      ||||

   1. V části **koncové body**určete požadované adresy URL, které se mají použít pro posílání zpráv akce a potvrzení.

      | Vlastnost | Požaduje se | Popis |
      |----------|----------|-------------|
      | **Adresa URL akce** |  Ano | Adresa URL, která se má použít pro posílání zpráv akce Adresa URL je povinné pole pro synchronní i asynchronní zprávy. |
      | **Potvrzovací adresa URL** | Ano | Adresa URL, která se má použít pro odesílání potvrzovacích zpráv. Adresa URL je povinné pole pro asynchronní zprávy. |
      ||||

1. Pokud chcete nastavit smlouvu s odkazy na RosettaNet PIP pro partnery, vyberte **odkazy ROSETTANET PIP**. V části **název PIP**vyberte název dříve vytvořeného PIP.

   ![Odkazy PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   Váš výběr naplní zbývající vlastnosti, které jsou založené na PIP, kterou jste nastavili ve vašem účtu pro integraci. V případě potřeby můžete změnit **roli PIP**.

   ![Vybraný PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

Po dokončení těchto kroků jste připraveni odesílat nebo přijímat zprávy RosettaNet.

## <a name="rosettanet-templates"></a>Šablony RosettaNet

Chcete-li zrychlit vývoj a doporučit způsoby integrace, můžete použít šablony aplikace logiky pro dekódování a kódování zpráv RosettaNet. Když vytvoříte aplikaci logiky, můžete v návrháři aplikace logiky vybrat z galerie šablon. Tyto šablony můžete také najít v [úložišti GitHub pro Azure Logic Apps](https://github.com/Azure/logicapps).

![Šablony RosettaNet](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>Přijímání a dekódování zpráv RosettaNet

1. [Vytvořte prázdnou aplikaci logiky](quickstart-create-first-logic-app-workflow.md).

1. [Propojte účet pro integraci](logic-apps-enterprise-integration-create-integration-account.md#link-account) s vaší aplikací logiky.

1. Než budete moct přidat akci dekódovat zprávu RosettaNet, musíte přidat Trigger pro spuštění aplikace logiky, jako je například Trigger žádosti.

1. Po přidání triggeru vyberte **Nový krok**.

   ![Přidat aktivační událost žádosti](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Do vyhledávacího pole zadejte "RosettaNet" a vyberte tuto akci: **RosettaNet dekódování**

   ![Najít a vybrat akci "RosettaNet dekódování"](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Zadejte informace o vlastnostech akce:

   ![Snímek obrazovky, který ukazuje, kde poskytnete informace pro vlastnosti akce](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Zpráva** | Ano | Zpráva RosettaNet k dekódování  |
   | **Hlavičky** | Ano | Hlavičky protokolu HTTP, které poskytují hodnoty pro verzi, což je verze RNIF, a typ odpovědi, který označuje typ komunikace mezi partnery a může být synchronní nebo asynchronní |
   | **Role** | Ano | Role hostitelského partnera v PIP |
   ||||

   Z akce dekódovat RosettaNet (výstup) spolu s dalšími vlastnostmi zahrnuje **odchozí signál**, který můžete vybrat ke kódování a návrat k partnerovi nebo provést jakoukoli jinou akci s tímto výstupem.

## <a name="send-or-encode-rosettanet-messages"></a>Posílání nebo kódování zpráv RosettaNet

1. [Vytvořte prázdnou aplikaci logiky](quickstart-create-first-logic-app-workflow.md).

1. [Propojte účet pro integraci](logic-apps-enterprise-integration-create-integration-account.md#link-account) s vaší aplikací logiky.

1. Než budete moct přidat akci ke kódování zprávy RosettaNet, musíte přidat Trigger pro spuštění aplikace logiky, jako je například Trigger žádosti.

1. Po přidání triggeru vyberte **Nový krok**.

   ![Přidat aktivační událost žádosti](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Do vyhledávacího pole zadejte "RosettaNet" a vyberte tuto akci: **RosettaNet kódování**

   ![Najít a vybrat akci "RosettaNet encode"](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Zadejte informace o vlastnostech akce:

   ![Zadání podrobností o akci](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Zpráva** | Ano | Zpráva RosettaNet ke kódování  |
   | **Partner hostitele** | Ano | Název hostitelského partnera |
   | **Partner hosta** | Ano | Název partnerského serveru hosta |
   | **Kód PIP** | Ano | Kód PIP |
   | **Verze PIP** | Ano | Verze PIP |  
   | **Identita instance PIP** | Ano | Jedinečný identifikátor pro tuto zprávu PIP |  
   | **Typ zprávy** | Ano | Typ zprávy, která se má zakódovat |  
   | **Role** | Ano | Role hostitelského partnera |
   ||||

   Zakódovaná zpráva je nyní připravena k odeslání partnerovi.

1. K odeslání kódované zprávy v tomto příkladu se používá akce **http** , která je přejmenována na partnerských zprávách odesílaných pomocí protokolu HTTP.

   ![Akce HTTP pro odeslání zprávy RosettaNet](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   Za RosettaNet standardy se obchodní transakce považují za kompletní, jenom když jsou dokončené všechny kroky, které jsou definovány v PIP.

1. Poté, co hostitel pošle zakódovanou zprávu partnerovi, počká na signál a potvrzení. K provedení této úlohy přidejte akci **RosettaNet čekání na odpověď** .

   ![Přidat akci "RosettaNet čekání na odpověď"](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   Doba, která se má použít pro čekání a počet opakovaných pokusů vychází z konfigurace PIP v účtu integrace. Pokud odpověď neobdrží, tato akce vygeneruje oznámení o selhání. Chcete-li zpracovat opakované pokusy, vždy vložte akce **kódování** a **čekání na odpověď** v rámci smyčky **do** .

   ![Do smyčky with RosettaNet Actions](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak ověřit, transformovat a další operace se zprávami pomocí [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
