---
title: RosettaNet zprávy pro podnikovou integraci B2B – Azure Logic Apps
description: Výměna zpráv RosettaNet v Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 88e02f3fbbca8007fdf479bb973f50c42a878d6e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332682"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Výměna zpráv RosettaNet pro podnikovou integraci B2B v Azure Logic Apps 

[RosettaNet](https://resources.gs1us.org) je nezisková consortium, který byl vytvořen standardních procesů pro sdílení obchodních informací. Tyto normy se obvykle používají pro procesy dodavatelského řetězce a se plánuje v oborech semiconductor electronics a logistiky. RosettaNet consortium vytváří a udržuje partnera rozhraní procesy (jadérka), která mají společné definice obchodní proces pro všechny výměny zpráv RosettaNet. RosettaNet je založené na XML a definuje zprávy, pokyny pro rozhraní pro obchodní procesy a implementaci architektur pro komunikaci mezi společnostmi.

V [Azure Logic Apps](../logic-apps/logic-apps-overview.md), konektor RosettaNet vám pomůže vytvořit integrační řešení, které podporují RosettaNet standardy. Konektor je založena na RosettaNet implementace rozhraní (RNIF) verze 2.0.01. RNIF je aplikační rozhraní open sítě, která umožňuje obchodním partnerům umožní spolupracovat spustit RosettaNet jadérka. Toto rozhraní definuje strukturu zprávy, potřeba potvrzování, kódování Multipurpose Internet Mail Extensions (MIME) a digitální podpis.

Konkrétně tento konektor poskytuje tyto možnosti:

* Zakódujte nebo přijímat zprávy RosettaNet.
* Dekódování nebo odesílání zpráv RosettaNet.
* Čekat na odpověď a generování oznámení o selhání.

Konektor pro tyto funkce podporuje všechny jadérka, které jsou definovány RNIF 2.0.01. Komunikace s partnerem, může být synchronní nebo asynchronní.

## <a name="rosettanet-concepts"></a>Koncepty RosettaNet

Tady jsou některé koncepce a termíny, které jsou jedinečné pro specifikaci RosettaNet a jsou důležité při vytváření na základě RosettaNet integrace:

* **PIP**

  RosettaNet organizace vytváří a udržuje partnera rozhraní procesy (jadérka), která mají společné definice obchodní proces pro všechny výměny zpráv RosettaNet. Každá specifikace PIP poskytuje soubor dokumentu typ definice (DTD) a obecných zásad dokumentu zprávy. Souboru DTD definuje strukturu zpráva obsahu služby. Zpráva pravidlo dokument, který je čitelný soubor HTML, určuje element úrovni omezení. Tyto soubory společně poskytují kompletní definici obchodních procesů.

   Jadérka jsou rozdělené podle vysoké úrovně obchodní funkce, nebo clusteru a podfunkce je nebo segmentu. Například "3A4" je PIP pro nákupní objednávky, zatímco "3" je funkce Správa objednávek a "3A" je nabídka & objednávky položku podfunkce je. Další informace najdete v tématu [RosettaNet lokality](https://resources.gs1us.org).

* **Akce**

  Část programu PIP, akce zprávy jsou obchodní zprávy, které se vyměňují mezi partnery.

* **Signál**

   Část programu PIP, signál zprávy se potvrzení, které se odesílají v reakci na akce zprávy.

* **Jednu akci a double akce**

  Pro akce jedním PIP je pouze odpověď signál zprávu o potvrzení. Pro akce double PIP iniciátor obdrží zprávu odpovědi a zašle odpověď s potvrzení vedle zprávy jedné akce.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte ještě předplatné Azure [zaregistrovat si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) k vaší smlouvě a jiných artefaktů B2B. Tato integrační účet musí být přidružený k vašemu předplatnému Azure.

* Alespoň dva [partneři](../logic-apps/logic-apps-enterprise-integration-partners.md) , které jsou definovány v účtu integrace a nakonfigurovanou kvalifikátor "DUNS" v části **obchodní identity**

* Konfigurace procesu PIP, který je nutný k odeslání nebo přijetí zprávy RosettaNet v účtu integrace. Konfigurace procesu ukládá všechny vlastnosti konfigurace PIP. Tato konfigurace může odkazovat potom při vytvořili smlouvu s partnerem. Vytvoření konfigurace procesu PIP v účtu integrace najdete v tématu [konfigurace procesu přidat PIP](#add-pip).

* Volitelné [certifikáty](../logic-apps/logic-apps-enterprise-integration-certificates.md) pro šifrování, dešifrování a podepisování zpráv, které můžete odeslat do účtu pro integraci. Certifikáty jsou vyžadovány pouze v případě použití podepisování nebo šifrování.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>Přidat konfiguraci procesu PIP

Pokud chcete přidat PIP procesu konfigurace účtu pro integraci, postupujte takto:

1. V [webu Azure portal](https://portal.azure.com), vyhledání a otevření účtu pro integraci.

1. Na **přehled** podokně, vyberte **RosettaNet PIP** dlaždici.

   ![Zvolte dlaždici RosettaNet](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. V části **RosettaNet PIP**, zvolte **přidat**. Zadejte podrobnosti o vašem PIP.

   ![Přidáním podrobností o RosettaNet PIP](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název** | Ano | Název vašeho PIP |
   | **Kód PIP** | Ano | Tři 6místným číselným kódem PIP. Další informace najdete v tématu [RosettaNet jadérka](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **Verzi PIP** | Ano | Číslo verze PIP, který je k dispozici na základě vaší vybrané PIP kódu |
   ||||

   Další informace o těchto vlastnostech PIP, najdete [RosettaNet webu](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg).

1. Jakmile budete hotovi, zvolte **OK**, který se vytvoří konfigurace PIP.

1. Chcete-li zobrazit nebo upravit konfiguraci procesu, vyberte PIP a zvolte **upravit jako JSON**.

   Všechna procesu konfigurace, které pochází ze specifikací PIP nastavení. Logic Apps naplní většinu nastavení s výchozími hodnotami, které jsou nejčastěji používané hodnoty těchto vlastností.

   ![Upravit konfiguraci RosettaNet PIP](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Zkontrolujte, jestli nastavení odpovídají hodnotám v příslušné specifikaci PIP a svých obchodních potřeb. V případě potřeby aktualizujte hodnoty ve formátu JSON a tyto změny uložit.

## <a name="create-rosettanet-agreement"></a>Vytvoření smlouvy RosettaNet

1. V [webu Azure portal](https://portal.azure.com), najít a otevřete svůj účet integrace, pokud není otevřen.

1. Na **přehled** podokně, vyberte **smlouvy** dlaždici.

   ![Zvolte dlaždici smlouvy](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. V části **smlouvy**, zvolte **přidat**. Zadejte podrobnosti o vaší smlouvě.

   ![Přidat podrobnosti smlouvy](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název** | Ano | Název smlouvy |
   | **Typ smlouvy** | Ano | Vyberte **RosettaNet**. |
   | **Host Partner** | Ano | Smlouva vyžaduje hostitele i hosta partnera. Partner hostitele představuje organizace, který konfiguruje smlouvy. |
   | **Identita hostitele** | Ano | Identifikátor pro hostitele partnera |
   | **Partner s identitou hosta** | Ano | Smlouva vyžaduje hostitele i hosta partnera. Partner s identitou hosta představuje organizace, která je podnikající s partnerem hostitele. |
   | **Identita hosta** | Ano | Identifikátor partner s identitou hosta |
   | **Zobrazit nastavení** | Různé | Tyto vlastnosti se vztahují na všechny zprávy přijaté službou hostitele partnera |
   | **Nastavení odesílání** | Různé | Tyto vlastnosti se vztahují na všechny zprávy odeslané partner hostitele |  
   | **Odkazy RosettaNet PIP** | Ano | Odkazy PIP smlouvy. Všechny zprávy RosettaNet musí být nakonfigurovaná PIP. |
   ||||

1. Chcete-li nastavit vaši smlouvu pro příjem příchozích zpráv od partnera hosta, vyberte **přijímat nastavení**.

   ![Zobrazit nastavení](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. V části povolit podepisování nebo šifrování pro příchozí zprávy **zprávy**vyberte **zprávu je nutné podepsat** nebo **zprávu je nutné zašifrovat** v uvedeném pořadí.

      | Vlastnost | Požaduje se | Popis |
      |----------|----------|-------------|
      | **Zprávu je nutné podepsat** | Ne | Podepište příchozí zprávy ve vybraném certifikátu. |
      | **Certifikát** | Ano, pokud je povoleno podepisování | Certifikát, který chcete použít pro podepisování |
      | **Povolit šifrování zpráv** | Ne | Šifrování příchozích zpráv ve vybraném certifikátu. |
      | **Certifikát** | Ano, pokud je povolené šifrování | Certifikát, který chcete použít pro šifrování |
      ||||

   1. V části každý výběr, vyberte příslušné [certifikát](./logic-apps-enterprise-integration-certificates.md), který jste dříve přidali do účtu pro integraci, použít pro podepisování nebo šifrování.

1. Chcete-li nastavit vaši smlouvu pro odesílání zpráv partner s identitou hosta, vyberte **odeslat nastavení**.

   ![Nastavení odesílání](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. V části povolit podepisování nebo šifrování pro odchozí zprávy **zprávy**vyberte **povolit podepisování zpráv** nebo **povolit šifrování zpráv** v uvedeném pořadí. V části každý výběr, vyberte příslušné algoritmus a [certifikát](./logic-apps-enterprise-integration-certificates.md), který jste dříve přidali do účtu pro integraci, použít pro podepisování nebo šifrování.

      | Vlastnost | Požaduje se | Popis |
      |----------|----------|-------------|
      | **Povolit podepisování zpráv** | Ne | Podepsání odchozích zpráv s vybranou podpisový algoritmus a certifikátem. |
      | **Podpisový algoritmus** | Ano, pokud je povoleno podepisování | Podpisový algoritmus, který chcete použít, na základě vybraného certifikátu |
      | **Certifikát** | Ano, pokud je povoleno podepisování | Certifikát, který chcete použít pro podepisování |
      | **Povolit šifrování zpráv** | Ne | Šifrování, odchozí s vybranou šifrovací algoritmus a certifikátem. |
      | **Šifrovací algoritmus** | Ano, pokud je povolené šifrování | Šifrovací algoritmus, který chcete použít, na základě vybraného certifikátu |
      | **Certifikát** | Ano, pokud je povolené šifrování | Certifikát, který chcete použít pro šifrování |
      ||||

   1. V části **koncové body**, zadejte požadované adresy URL pro odeslání zprávy akce a potvrzování.

      | Vlastnost | Požaduje se | Popis |
      |----------|----------|-------------|
      | **Adresa URL akce** |  Ano | Adresa URL pro použití pro odesílání zpráv akce. Adresa URL je povinné pole pro synchronní a asynchronní zprávy. |
      | **Adresa URL potvrzení** | Ano | Adresa URL pro použití pro odesílání zpráv potvrzení. Adresa URL je povinné pole pro asynchronní zprávy. |
      ||||

1. Chcete-li nastavit smlouvu pro partnery s odkazy na RosettaNet PIP, vyberte **odkazuje RosettaNet PIP**. V části **název PIP**, vyberte název vytvořeného PIP.

   ![Odkazy na PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   Váš výběr naplní zbývající vlastnosti, které jsou založeny na PIP, který jste vytvořili v účtu integrace. Pokud třeba, můžete změnit **PIP Role**.

   ![Vybrané PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

Po dokončení těchto kroků budete připraveni k odesílání nebo příjmu zpráv RosettaNet.

## <a name="rosettanet-templates"></a>RosettaNet šablony

K urychlení vývoje a vzorů integrace doporučení, můžete použít šablony aplikace logiky pro zprávy RosettaNet kódování a dekódování. Když vytvoříte aplikaci logiky, můžete vybrat z Galerie šablon v návrháři aplikace logiky. Můžete také vyhledat všechny šablony [úložiště GitHub pro Azure Logic Apps](https://github.com/Azure/logicapps).

![RosettaNet šablony](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>Zobrazí nebo dekódování RosettaNet zprávy

1. [Vytvoření prázdné aplikace logiky](quickstart-create-first-logic-app-workflow.md).

1. [Propojte si svůj účet integrace](logic-apps-enterprise-integration-create-integration-account.md#link-account) do aplikace logiky.

1. Než přidáte akci, která dekódovat zprávu RosettaNet, je nutné přidat aktivační událost pro spuštění aplikace logiky, jako je například aktivační událost požadavek.

1. Po přidání triggeru, zvolte **nový krok**.

   ![Přidání triggeru požadavku](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Do vyhledávacího pole zadejte "rosettanet" a vyberte tuto akci: **Dekódování RosettaNet**

   ![Vyhledejte a vyberte akci "RosettaNet dekódovat"](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Zadejte informace pro vlastnosti akce:

   ![Zadejte podrobnosti akce](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Zpráva** | Ano | Dekódovat zprávu RosettaNet  |
   | **Hlavičky** | Ano | Hlavičky protokolu HTTP, které obsahují hodnoty pro verzi, což je verze RNIF, a typ odpovědi, který určuje typ komunikace mezi partnery a může být synchronní nebo asynchronní |
   | **Role** | Ano | Roli partnera poskytujícího hostitele v PIP |
   ||||

   Akce RosettaNet dekódování výstup, společně s další vlastnosti obsahuje **odchozí signál**, které je možné kódovat a vraťte se zpět do partnera nebo provádět jiné akce na výstupu.

## <a name="send-or-encode-rosettanet-messages"></a>Odeslání nebo kódování RosettaNet zprávy

1. [Vytvoření prázdné aplikace logiky](quickstart-create-first-logic-app-workflow.md).

1. [Propojte si svůj účet integrace](logic-apps-enterprise-integration-create-integration-account.md#link-account) do aplikace logiky.

1. Než přidáte akci k zakódování zprávy RosettaNet, je nutné přidat aktivační událost pro spuštění aplikace logiky, jako je například aktivační událost požadavek.

1. Po přidání triggeru, zvolte **nový krok**.

   ![Přidání triggeru požadavku](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Do vyhledávacího pole zadejte "rosettanet" a vyberte tuto akci: **RosettaNet Encode**

   ![Vyhledejte a vyberte akci "RosettaNet kódování"](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Zadejte informace pro vlastnosti akce:

   ![Zadejte podrobnosti akce](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Zpráva** | Ano | Zpráva RosettaNet ke kódování  |
   | **Partner s identitou hostitele** | Ano | Název hostitele partnera |
   | **Partner s identitou hosta** | Ano | Jméno partnera hosta |
   | **Kód PIP** | Ano | Kód PIP |
   | **Verzi PIP** | Ano | Verzi PIP |  
   | **Identita instance PIP** | Ano | Jedinečný identifikátor pro tuto zprávu PIP |  
   | **Typ zprávy** | Ano | Typ zprávy ke kódování |  
   | **Role** | Ano | Role partner s identitou hostitele |
   ||||

   Zakódovaná zpráva je nyní připraven k odeslání partnera.

1. K odeslání kódovaného zprávy, v tomto příkladu **HTTP** akce, která je přejmenován "HTTP - kódování odešle zprávu partner".

   ![Akce HTTP pro odeslání zprávy RosettaNet](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   Podle standardů RosettaNet obchodních transakcí se považuje za dokončené pouze v případě, že se splnily všechny kroky, které jsou definované pomocí PIP.

1. Poté, co hostitel odešle kódované zprávy na partnera, hostitele čeká na signál a potvrzení. Chcete-li provést tuto úlohu, přidejte **RosettaNet čekat na odpověď** akce.

   ![Přidání akce "RosettaNet čekat na odpověď"](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   Doba trvání pro čekání a počet opakování jsou založené na konfiguraci PIP v účtu integrace. Pokud odpověď přijata nebude, tato akce vytvoří selhání oznámení. Zpracování opakovaných pokusů, vždy umístěte **kódovat** a **čeká na odpověď** akce v **dokud** smyčky.

   ![Až do smyčky s akcemi RosettaNet](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Další postup

* Zjistěte, jak ověřit, transformaci a další operace zprávu s [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
