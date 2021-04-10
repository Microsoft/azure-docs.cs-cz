---
title: Vytváření pracovních postupů ve verzi Preview Logic Apps v Azure Portal
description: Sestavujte a spouštějte pracovní postupy pro scénáře automatizace a integrace pomocí služby Azure Logic Apps Preview v Azure Portal.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: ff938d29d998b6fcf0b2cfae72a9a9e685a10dc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563936"
---
# <a name="create-stateful-and-stateless-workflows-in-the-azure-portal-with-azure-logic-apps-preview"></a>Vytváření stavových a bezstavových pracovních postupů v Azure Portal s využitím Azure Logic Apps Preview

> [!IMPORTANT]
> Tato funkce je ve verzi Public Preview, poskytuje se bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V [Azure Logic Apps Preview](logic-apps-overview-preview.md)můžete vytvářet řešení pro automatizaci a integraci napříč aplikacemi, daty, Cloud Services a systémy vytvořením a spuštěním Logic Apps, které zahrnují [ *stavové* a *bezstavové* pracovní postupy](logic-apps-overview-preview.md#stateful-stateless) v Azure Portal, od nového typu prostředku **Aplikace logiky (Preview)** . Díky tomuto novému typu aplikace logiky můžete vytvořit několik pracovních postupů využívajících přepracované prostředí runtime Azure Logic Apps Preview, které poskytuje přenositelnost, lepší výkon a flexibilitu pro nasazení a spouštění v různých hostujících prostředích, nejen Azure, ale i kontejnery Docker. Další informace o novém typu aplikace logiky najdete v tématu [přehled Azure Logic Apps Preview](logic-apps-overview-preview.md).

![Snímek obrazovky zobrazující Azure Portal pomocí návrháře pracovních postupů pro prostředek aplikace logiky (Preview)](./media/create-stateful-stateless-workflows-azure-portal/azure-portal-logic-apps-overview.png)

V Azure Portal můžete začít vytvořením nového prostředku **Aplikace logiky (Preview)** . I když můžete začít tím, že [vytvoříte projekt v Visual Studio Code s rozšířením Azure Logic Apps (Preview)](create-stateful-stateless-workflows-visual-studio-code.md), oba přístupy poskytují možnost nasadit a spustit aplikaci logiky ve stejných druzích hostujících prostředí.

Mezitím můžete stále vytvořit původní typ aplikace logiky. I když se vývojové prostředí na portálu liší od původní a nové typy aplikací logiky, může vaše předplatné Azure zahrnovat oba typy. Můžete zobrazit a přistupovat ke všem nasazeným Logic Apps v předplatném Azure, ale aplikace se uspořádají do jejich vlastních kategorií a oddílů.

Tento článek ukazuje, jak vytvořit aplikaci logiky a pracovní postup v Azure Portal pomocí typu prostředku **Aplikace logiky (Preview)** a provádění těchto úloh na vysoké úrovni:

* Vytvořte nový prostředek aplikace logiky a přidejte prázdný pracovní postup.

* Přidejte Trigger a akci.

* Aktivovat spuštění pracovního postupu.

* Zobrazit historii spuštění a triggeru pracovního postupu.

* Po nasazení povolte nebo otevřete Application Insights.

* Povolí historii spuštění bezstavových pracovních postupů.

> [!NOTE]
> Informace o aktuálních známých problémech najdete na [stránce Logic Apps Public Preview známé problémy v GitHubu](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

## <a name="prerequisites"></a>Požadavky

* Účet a předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* [Účet Azure Storage](../storage/common/storage-account-overview.md) , protože prostředek **Aplikace logiky (Preview)** je napájený pomocí Azure functions a má [požadavky na úložiště, které se podobají funkcím aplikací](../azure-functions/storage-considerations.md). Můžete použít existující účet úložiště, nebo můžete vytvořit účet úložiště předem nebo během vytváření aplikace logiky.

  > [!NOTE]
  > [Stavové aplikace logiky](logic-apps-overview-preview.md#stateful-stateless) provádějí transakce úložiště, například používání front pro plánování a ukládání stavů pracovního postupu v tabulkách a objektech blob. Tyto transakce se účtují [za Azure Storage poplatky](https://azure.microsoft.com/pricing/details/storage/). Další informace o tom, jak stavové aplikace logiky ukládají data v externích úložištích, najdete v tématu stavová [versus Bezstavová](logic-apps-overview-preview.md#stateful-stateless).

* K nasazení do kontejneru Docker budete potřebovat existující image kontejneru Docker. Tuto image můžete například vytvořit pomocí [Azure Container Registry](../container-registry/container-registry-intro.md), [App Service](../app-service/overview.md)nebo [instance kontejneru Azure](../container-instances/container-instances-overview.md). 

* Pokud chcete v tomto článku sestavit stejnou ukázkovou aplikaci logiky, potřebujete e-mailový účet Office 365 Outlook, který pro přihlášení používá pracovní nebo školní účet Microsoft.

  Pokud se rozhodnete použít jiný [e-mailový konektor, který podporuje Azure Logic Apps](/connectors/), jako je například Outlook.com nebo [Gmail](../connectors/connectors-google-data-security-privacy-policy.md), můžete postupovat dál a obecné kroky jsou stejné, ale vaše uživatelské rozhraní a možnosti se můžou v některých případech lišit. Pokud například použijete konektor Outlook.com, použijte místo toho osobní účet Microsoft k přihlášení.

* Chcete-li otestovat ukázkovou aplikaci logiky, kterou vytvoříte v tomto článku, budete potřebovat nástroj, který může odeslat volání triggeru žádosti, což je první krok v ukázkové aplikaci logiky. Pokud tento nástroj nemáte, můžete si ho stáhnout, nainstalovat [a používat.](https://www.postman.com/downloads/)

* Pokud vytvoříte aplikaci logiky s nastavením, které podporuje použití [Application Insights](../azure-monitor/app/app-insights-overview.md), můžete volitelně povolit diagnostické protokolování a trasování pro vaši aplikaci logiky. Můžete to udělat buď při vytváření aplikace logiky, nebo po nasazení. Musíte mít instanci Application Insights, ale tento prostředek můžete vytvořit buď [předem](../azure-monitor/app/create-workspace-resource.md), při vytváření aplikace logiky nebo po nasazení.

## <a name="create-the-logic-app-resource"></a>Vytvoření prostředku aplikace logiky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. Do vyhledávacího pole Azure Portal zadejte `logic app preview` a vyberte **Aplikace logiky (Preview)**.

   ![Snímek obrazovky, který zobrazuje vyhledávací pole Azure Portal s hledaným termínem "Logic App Preview" a "vybraným prostředkem" aplikace logiky (Preview).](./media/create-stateful-stateless-workflows-azure-portal/find-logic-app-resource-template.png)

1. Na stránce **Aplikace logiky (Preview)** vyberte **Přidat**.

1. Na stránce **Vytvoření aplikace logiky (Preview)** zadejte na kartě **základy** tyto informace o vaší aplikaci logiky.

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Předplatné** | Yes | <*Azure – předplatné – název*> | Předplatné Azure, které se má použít pro vaši aplikaci logiky. |
   | **Skupina prostředků** | Yes | <*Azure-Resource-Group-Name*> | Skupina prostředků Azure, kde vytvoříte aplikaci logiky a související prostředky. Název tohoto prostředku musí být v různých oblastech jedinečný a může obsahovat jenom písmena, číslice, spojovníky ( **-** ), podtržítka (**_**), kulaté závorky (**()**) a tečky (**.**). <p><p>Tento příklad vytvoří skupinu prostředků s názvem `Fabrikam-Workflows-RG` . |
   | **Název aplikace logiky** | Yes | <*Logic-App-Name*> | Název, který se má použít pro vaši aplikaci logiky. Název tohoto prostředku musí být v různých oblastech jedinečný a může obsahovat jenom písmena, číslice, spojovníky ( **-** ), podtržítka (**_**), kulaté závorky (**()**) a tečky (**.**). <p><p>Tento příklad vytvoří aplikaci logiky s názvem `Fabrikam-Workflows` . <p><p>**Poznámka**: název vaší aplikace logiky automaticky získá příponu, `.azurewebsites.net` , protože prostředek **Aplikace logiky (Preview)** je napájený Azure Functions, který používá stejné zásady vytváření názvů aplikací. |
   | **Publikovat** | Yes | <*nasazení – prostředí*> | Cíl nasazení pro vaši aplikaci logiky. Do Azure se dá nasadit tak, že vyberete **pracovní postup** nebo **kontejner Docker**. <p><p>V tomto příkladu se používá **pracovní postup**, který nasadí prostředek **Aplikace logiky (Preview)** do Azure Portal. <p><p>**Poznámka**: před výběrem **kontejneru Docker** se ujistěte, že jste vytvořili image kontejneru Docker. Tuto image můžete například vytvořit pomocí [Azure Container Registry](../container-registry/container-registry-intro.md), [App Service](../app-service/overview.md)nebo [instance kontejneru Azure](../container-instances/container-instances-overview.md). Tímto způsobem můžete po výběru **kontejneru Docker** [zadat kontejner, který chcete použít v nastavení aplikace logiky](#set-docker-container). |
   | **Oblast** | Yes | <*Oblast Azure*> | Oblast Azure, která se má použít při vytváření skupiny prostředků a prostředků <p><p>Tento příklad používá **západní USA**. |
   |||||

   Tady je příklad:

   ![Snímek obrazovky zobrazující stránku Azure Portal a "Vytvoření aplikace logiky (Preview)".](./media/create-stateful-stateless-workflows-azure-portal/create-logic-app-resource-portal.png)

1. Dále na kartě **hosting** zadejte informace o řešení úložiště a plánu hostování, který se má použít pro vaši aplikaci logiky.

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Účet úložiště** | Yes | <*Azure-Storage-Account-Name*> | [Účet Azure Storage](../storage/common/storage-account-overview.md) , který se má použít pro transakce úložiště. Tento název prostředku musí být v různých oblastech jedinečný a musí mít 3-24 znaků a obsahovat jenom číslice a malá písmena. Vyberte buď existující účet, nebo vytvořte nový účet. <p><p>Tento příklad vytvoří účet úložiště s názvem `fabrikamstorageacct` . |
   | **Typ plánu** | Yes | <*Azure – hostování – plán*> | [Plán hostování](../app-service/overview-hosting-plans.md) , který se má použít pro nasazení aplikace logiky, což je buď [**Functions Premium**](../azure-functions/functions-premium-plan.md) , nebo [ **plán služby App Service** (vyhrazená)](../azure-functions/dedicated-plan.md). Vaše volba má vliv na možnosti a cenové úrovně, které jsou později dostupné. <p><p>V tomto příkladu se používá **plán služby App Service**. <p><p>**Poznámka**: podobně jako u Azure Functions typ prostředku **Aplikace logiky (Preview)** vyžaduje plán hostování a cenovou úroveň. Plány spotřeby nejsou podporované ani nejsou dostupné pro tento typ prostředku. Další informace najdete v těchto tématech: <p><p>- [Azure Functions škálování a hostování](../azure-functions/functions-scale.md) <br>- [Podrobnosti o cenách App Service](https://azure.microsoft.com/pricing/details/app-service/) <p><p>Například plán služby Functions úrovně Premium poskytuje přístup k funkcím sítě, jako je například připojení a integrace soukromě s virtuálními sítěmi Azure, podobně jako Azure Functions při vytváření a nasazování aplikací logiky. Další informace najdete v těchto tématech: <p><p>- [Možnosti Azure Functions sítě](../azure-functions/functions-networking-options.md) <br>- [Azure Logic Apps spouštění možností kdekoli v síti pomocí Azure Logic Apps Preview](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047) |
   | **Plán Windows** | Yes | <*plán – název*> | Název plánu, který se má použít. Vyberte buď existující plán, nebo zadejte název nového plánu. <p><p>V tomto příkladu se používá název `Fabrikam-Service-Plan` . |
   | **SKU a velikost** | Yes | <*cenová úroveň*> | [Cenová úroveň](../app-service/overview-hosting-plans.md) , která se má použít pro hostování aplikace logiky Vaše volby jsou ovlivněny typem plánu, který jste předtím zvolili. Pokud chcete změnit výchozí úroveň, vyberte **změnit velikost**. Na základě zatížení, které potřebujete, pak můžete vybrat jiné cenové úrovně. <p><p>V tomto příkladu se používá Volná **cenová úroveň F1** pro úlohy **vývoje a testování** . Další informace najdete v [podrobnostech o cenách App Service](https://azure.microsoft.com/pricing/details/app-service/). |
   |||||

1. Pokud vaše nastavení vytváření a nasazení podporují použití [Application Insights](../azure-monitor/app/app-insights-overview.md), můžete volitelně povolit diagnostické protokolování a trasování pro vaši aplikaci logiky.

   1. Na kartě **monitorování** v části **Application Insights** nastavte **možnost Povolit Application Insights** na **Ano** , pokud ještě není vybraná.

   1. Pro nastavení **Application Insights** vyberte buď existující instanci Application Insights, nebo pokud chcete vytvořit novou instanci, vyberte **vytvořit novou** a zadejte název, který chcete použít.

1. Až Azure ověří nastavení vaší aplikace logiky, na kartě **Revize + vytvořit** vyberte **vytvořit**.

   Například:

   ![Snímek obrazovky zobrazující Azure Portal a nové nastavení prostředků aplikace logiky](./media/create-stateful-stateless-workflows-azure-portal/check-logic-app-resource-settings.png)

   > [!TIP]
   > Pokud se zobrazí chyba ověřování po výběru **vytvořit**, otevřete a Projděte si podrobnosti o chybě. Pokud vaše vybraná oblast například dosáhne kvóty pro prostředky, které se pokoušíte vytvořit, možná budete muset vyzkoušet jinou oblast.

   Až Azure dokončí nasazení, vaše aplikace logiky se automaticky zalive a spustí, ale ještě nic nedělá, protože žádné pracovní postupy neexistují.

1. Na stránce dokončení nasazení vyberte **Přejít k prostředku** , abyste mohli začít sestavovat pracovní postup. Pokud jste vybrali **Docker Container** pro nasazení aplikace logiky, pokračujte postupem, který [poskytuje informace o daném kontejneru Docker](#set-docker-container).

   ![Snímek obrazovky zobrazující Azure Portal a dokončené nasazení.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-completed-deployment.png)

<a name="set-docker-container"></a>

## <a name="specify-docker-container-for-deployment"></a>Zadat kontejner Docker pro nasazení

Než začnete s tímto postupem, budete potřebovat image kontejneru Docker. Tuto image můžete například vytvořit pomocí [Azure Container Registry](../container-registry/container-registry-intro.md), [App Service](../app-service/overview.md)nebo [instance kontejneru Azure](../container-instances/container-instances-overview.md). Po vytvoření aplikace logiky pak můžete zadat informace o kontejneru Docker.

1. V Azure Portal přejít na prostředek aplikace logiky.

1. V nabídce aplikace logiky v části **Nastavení** vyberte **centrum nasazení**.

1. V podokně **centrum nasazení** postupujte podle pokynů pro poskytování a správu podrobností kontejneru Docker.

<a name="add-workflow"></a>

## <a name="add-a-blank-workflow"></a>Přidat prázdný pracovní postup

1. Po otevření prostředku Azure v nabídce aplikace logiky vyberte **pracovní postupy**. Na panelu nástrojů **pracovní postupy** vyberte **Přidat**.

   ![Snímek obrazovky, který zobrazuje nabídku prostředků aplikace logiky s vybranými pracovními postupy, a pak na panelu nástrojů je vybraná možnost Přidat.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-add-blank-workflow.png)

1. Po otevření podokna **nový pracovní postup** zadejte název pracovního postupu a vyberte [ **stavový** nebo **bezstavový**](logic-apps-overview-preview.md#stateful-stateless) pracovní postup. Po dokončení vyberte **Vytvořit**.

   Tento příklad přidá prázdný stavový pracovní postup s názvem `Fabrikam-Stateful-Workflow` . Ve výchozím nastavení je pracovní postup povolený, ale neprovádí žádnou akci, dokud nepřidáte Trigger a akce.

   ![Snímek obrazovky zobrazující nově přidaný prázdný stavový pracovní postup "Fabrikam-Stateful-Workflow".](./media/create-stateful-stateless-workflows-azure-portal/logic-app-blank-workflow-created.png)

1. V dalším kroku otevřete prázdný pracovní postup v návrháři, abyste mohli přidat Trigger a akci.

   1. V seznamu pracovní postup vyberte prázdný pracovní postup.

   1. V nabídce pracovní postup vyberte v části **vývojář** možnost **Návrhář**.

      Na návrhové ploše se již zobrazí příkazový řádek pro **výběr operace** , který je vybrán jako výchozí, aby se zobrazilo podokno **Přidat aktivační událost** , které se zobrazí také jako otevřené.

      ![Snímek obrazovky, který zobrazuje otevřeného návrháře pracovních postupů s volbou "zvolit operaci" vybraný na návrhové ploše.](./media/create-stateful-stateless-workflows-azure-portal/opened-logic-app-designer-blank-workflow.png)

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-an-action"></a>Přidání triggeru a akce

V tomto příkladu se vytvoří pracovní postup, který má tyto kroky:

* Vestavěná [aktivační událost žádosti](../connectors/connectors-native-reqres.md), **když se přijme požadavek HTTP**, který přijímá příchozí volání nebo požadavky a vytvoří koncový bod, který může volat jiné služby nebo Logic Apps.

* **Odeslání e-mailu** na [akci Office 365 Outlooku](../connectors/connectors-create-api-office365-outlook.md)

* Integrovaná [Akce odpovědi](../connectors/connectors-native-reqres.md), kterou použijete k odeslání odpovědi a vrácení dat zpět volajícímu.

### <a name="add-the-request-trigger"></a>Přidat aktivační událost žádosti

Než budete moct přidat Trigger do prázdného pracovního postupu, ujistěte se, že je Návrhář pracovního postupu otevřený a že se na návrhové ploše vybere příkazový řádek pro **výběr operace** .

1. Vedle návrhové plochy v podokně **Přidat aktivační událost** zaškrtněte v poli **zvolit hledání operace** , zda je vybraná **integrovaná** karta. Tato karta zobrazuje triggery, které se spouštějí nativně v Azure Logic Apps.

1. Do vyhledávacího pole **Zvolte operaci** zadejte `when a http request` a vyberte vestavěný aktivační událost žádosti, která se pojmenuje **při přijetí požadavku HTTP**.

   ![Snímek obrazovky znázorňující návrháře a * * přidat aktivační událost * * s názvem "když se přijme požadavek HTTP"](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger.png)

   Když se aktivační událost objeví v návrháři, otevře se podokno podrobností triggeru, ve kterém se zobrazí vlastnosti triggeru, nastavení a další akce.

   ![Snímek obrazovky, který zobrazuje návrháře s vybranou aktivační událostí při přijetí požadavku HTTP a otevření podokna podrobností triggeru](./media/create-stateful-stateless-workflows-azure-portal/request-trigger-added-to-designer.png)

   > [!TIP]
   > Pokud se podokno podrobností nezobrazí, ujistěte se, že je v Návrháři vybraný Trigger.

1. Pokud potřebujete odstranit položku z návrháře, [postupujte podle těchto kroků a odstraňte položky z návrháře](#delete-from-designer).

1. Pokud chcete svou práci uložit, na panelu nástrojů návrháře vyberte **Uložit**.

   Když pracovní postup uložíte poprvé a tento pracovní postup začíná triggerem požadavku, služba Logic Apps automaticky vygeneruje adresu URL pro koncový bod, který vytvořil Trigger žádosti. Později při testování pracovního postupu odešlete požadavek na tuto adresu URL, která aktivuje Trigger a spustí spuštění pracovního postupu.

### <a name="add-the-office-365-outlook-action"></a>Přidat akci sady Office 365 Outlook

1. V návrháři v rámci triggeru, který jste přidali, vyberte **Nový krok**.

   V návrháři se zobrazí výzva **Zvolit operaci** a otevře se podokno **přidat akci** , ve kterém můžete vybrat další akci.

   > [!NOTE]
   > Pokud se v podokně **přidat akci** zobrazí chybová zpráva "nelze načíst vlastnost" Filter undefined ", uložte pracovní postup, znovu načtěte pracovní postup, znovu otevřete pracovní postup a akci opakujte.

1. V podokně **přidat akci** vyberte v poli pro hledání **operace** možnost **Azure**. Tato karta zobrazuje spravované konektory, které jsou k dispozici a nasazeny v Azure.

   > [!NOTE]
   > Pokud se v podokně **přidat akci** zobrazí chybová zpráva, `The access token expiry UTC time '{token-expiration-date-time}' is earlier than current UTC time '{current-date-time}'` uložte pracovní postup, načtěte znovu stránku a znovu otevřete pracovní postup a zkuste akci přidat znovu.

   V tomto příkladu se používá akce Office 365 Outlook s názvem **Odeslat e-mail (v2)**.

   ![Snímek obrazovky, který zobrazuje návrháře a podokno * * přidat akci * * s vybranou akcí Office 365 Outlooku odeslat e-mail](./media/create-stateful-stateless-workflows-azure-portal/find-send-email-action.png)

1. V podokně podrobností akce na kartě **vytvořit připojení** vyberte **Přihlásit** , abyste mohli vytvořit připojení k e-mailovému účtu.

   ![Snímek obrazovky, který zobrazuje návrháře a podokno podrobností odeslat e-mail (v2) s vybraným možnost přihlásit](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-sign-in.png)

1. Až se vám zobrazí výzva k zadání souhlasu s přístupem k e-mailovému účtu, přihlaste se pomocí přihlašovacích údajů k účtu.

   > [!NOTE]
   > Pokud se zobrazí chybová zpráva, `Failed with error: 'The browser is closed.'. Please sign in again` Ověřte, zda váš prohlížeč blokuje soubory cookie třetích stran. Pokud jsou tyto soubory cookie blokované, zkuste je přidat `https://portal.azure.com` do seznamu webů, které můžou používat soubory cookie. Pokud používáte režim anonymním, ujistěte se, že soubory cookie třetích stran nejsou při práci v tomto režimu zablokované.
   > 
   > V případě potřeby znovu načtěte stránku, otevřete pracovní postup, znovu přidejte akci e-mailu a zkuste připojení vytvořit.

   Jakmile Azure vytvoří připojení, v návrháři se zobrazí akce **Odeslat e-mail** a vybere se ve výchozím nastavení. Pokud akce není vybrána, vyberte akci, aby její podokno podrobností bylo také otevřeno.

1. V podokně podrobností akce zadejte na kartě **parametry** požadované informace pro akci, například:

   ![Snímek obrazovky, který zobrazuje návrháře a podokno podrobností odeslání e-mailu s vybranou kartou Parameters (parametry)](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-details.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Do** | Yes | <*vaše e-mailová adresa*> | Příjemce e-mailu, který může být vaše e-mailová adresa pro testovací účely. V tomto příkladu se používá fiktivní e-mail, `sophiaowen@fabrikam.com` . |
   | **Předmět** | Yes | `An email from your example workflow` | Předmět e-mailu |
   | **Text** | Yes | `Hello from your example workflow!` | Obsah těla e-mailu |
   ||||

   > [!NOTE]
   > Při provádění změn v podokně podrobností na stránce **Nastavení**, **statický výsledek** nebo **Spustit po** kartách, nezapomeňte vybrat možnost **Hotovo** a potvrdit tyto změny před přepnutím tabulátorů nebo změnou fokusu na návrháře. V opačném případě Návrhář změny nezachová.

1. Uložte svou práci. Na panelu nástrojů návrháře vyberte **Uložit**.

1. Pokud má vaše prostředí striktní požadavky na síť nebo brány firewall, které omezují provoz, musíte nastavit oprávnění pro všechna připojení triggeru nebo akce, která existují ve vašem pracovním postupu. Vyhledání plně kvalifikovaného 

   V opačném případě otestujte pracovní postup [ručním spuštěním](#trigger-workflow)rutiny.

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>Vyhledat názvy domén pro přístup k bráně firewall

Než nasadíte aplikaci logiky a spustíte pracovní postup v Azure Portal, pokud má vaše prostředí striktní požadavky na síť nebo brány firewall, které omezují provoz, musíte nastavit síť nebo bránu firewall pro jakékoli připojení triggeru nebo akce v pracovních postupech, které existují ve vaší aplikaci logiky.

Plně kvalifikované názvy domén (FQDN) pro tato připojení zjistíte pomocí těchto kroků:

1. V nabídce aplikace logiky v části **pracovní postupy** vyberte **připojení**. Na kartě **připojení rozhraní API** vyberte název prostředku připojení, například:

   ![Snímek obrazovky, který zobrazuje nabídku aplikace Azure Portal a logiky se zvoleným názvem připojení a názvem prostředku "offic365".](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connections.png)

1. Rozšiřte si prohlížeč dostatečně natolik, aby se zobrazilo **zobrazení JSON** v pravém horním rohu prohlížeče, vyberte **zobrazení JSON**.

   ![Snímek obrazovky, který zobrazuje podokno připojení Azure Portal a rozhraní API s vybraným "zobrazením JSON".](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connection-view-json.png)

1. Najděte, zkopírujte a uložte `connectionRuntimeUrl` hodnotu vlastnosti někam bezpečně, abyste mohli bránu firewall nastavit s těmito informacemi.

   ![Snímek obrazovky, na které se zobrazuje vybraná hodnota vlastnosti "connectionRuntimeUrl".](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connection-runtime-url.png)

1. Pro každé připojení opakujte příslušné kroky.

<a name="trigger-workflow"></a>

## <a name="trigger-the-workflow"></a>Aktivovat pracovní postup

V tomto příkladu se pracovní postup spustí, když aktivační událost žádosti obdrží příchozí požadavek, který se pošle na adresu URL koncového bodu vytvořeného aktivační událostí. Při prvním uložení pracovního postupu služba Logic Apps automaticky vygenerovala tuto adresu URL. Takže před odesláním této žádosti o aktivaci pracovního postupu potřebujete najít tuto adresu URL.

1. V Návrháři pracovních postupů vyberte Trigger žádosti s názvem, **když se přijme požadavek HTTP**.

1. Po otevření podokna podrobností Najděte na kartě **parametry** vlastnost **Adresa URL http post** . Chcete-li zkopírovat vygenerovanou adresu URL, vyberte **adresu URL pro kopírování** (ikona kopírovat soubor) a uložte adresu URL někam jinde pro nyní. Adresa URL se řídí tímto formátem:

   `http://<logic-app-name>.azurewebsites.net:443/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

   ![Snímek obrazovky zobrazující návrháře s triggerem požadavku a adresou URL koncového bodu ve vlastnosti Adresa URL příspěvku HTTP](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger-url.png)

   V tomto příkladu vypadá adresa URL takto:

   `https://fabrikam-workflows.azurewebsites.net:443/api/Fabrikam-Stateful-Workflow/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxxxxXXXXxxxxxXXXXxxxXXXXxxxxXXXX`

   > [!TIP]
   > Adresu URL koncového bodu můžete najít také v podokně **přehledu** vaší aplikace logiky ve vlastnosti **Adresa URL pracovního postupu** .
   >
   > 1. V nabídce prostředek vyberte **Přehled**.
   > 1. V podokně **Přehled** vyhledejte vlastnost **Adresa URL pracovního postupu** .
   > 1. Pokud chcete zkopírovat adresu URL koncového bodu, přesuňte ukazatel na konec textu adresy URL koncového bodu a vyberte **Kopírovat do schránky** (ikona kopírovat soubor).

1. Chcete-li otestovat adresu URL odesláním žádosti, spusťte příkaz [post](https://www.postman.com/downloads/) nebo preferovaný nástroj pro vytváření a odesílání požadavků.

   Tento příklad pokračuje pomocí metody post. Další informace najdete v části [post Začínáme](https://learning.postman.com/docs/getting-started/introduction/).

   1. Na panelu nástrojů pro odeslání vyberte **Nový**.

      ![Snímek obrazovky, který zobrazuje příspěvek s vybraným tlačítkem nový](./media/create-stateful-stateless-workflows-azure-portal/postman-create-request.png)

   1. V podokně **vytvořit nový** v části **stavební bloky** vyberte **požadavek**.

   1. V okně **Uložit žádost** zadejte v části **název žádosti** název žádosti, například `Test workflow trigger` .

   1. V části **Vyberte kolekci nebo složku, do které chcete uložit**, vyberte **vytvořit kolekci**.

   1. V části **všechny kolekce** zadejte název kolekce, která se má vytvořit pro uspořádání vašich požadavků, stiskněte klávesu ENTER a vyberte **Uložit pro <*název* > kolekce**. Tento příklad používá `Logic Apps requests` jako název kolekce.

      Otevře se podokno žádosti vystavení, kde můžete poslat požadavek na adresu URL koncového bodu pro aktivační událost žádosti.

      ![Snímek obrazovky, který zobrazuje vydaný příspěvek v podokně otevřené žádosti](./media/create-stateful-stateless-workflows-azure-portal/postman-request-pane.png)

   1. V podokně žádosti v poli adresa, která se nachází vedle seznamu metod, který aktuálně zobrazuje metodu **Get** jako výchozí požadavek, vložte adresu URL, kterou jste zkopírovali dříve, a vyberte **Odeslat**.

      ![Snímek obrazovky, který zobrazuje adresu URL pro odesílání a koncové body v poli Adresa s vybraným tlačítkem Odeslat](./media/create-stateful-stateless-workflows-azure-portal/postman-test-endpoint-url.png)

      Když se Trigger aktivuje, ukázkový pracovní postup se spustí a pošle e-mail, který vypadá podobně jako v tomto příkladu:

      ![Snímek obrazovky, který zobrazuje e-mail Outlooku, jak je popsáno v příkladu](./media/create-stateful-stateless-workflows-azure-portal/workflow-app-result-email.png)

<a name="view-run-history"></a>

## <a name="review-run-history"></a>Kontrola historie spuštění

U stavového pracovního postupu můžete po každém spuštění pracovního postupu zobrazit historii spuštění, včetně stavu celkového spuštění, triggeru a pro každou akci společně s jejich vstupy a výstupy. V Azure Portal se historie spuštění a historie aktivačních událostí zobrazují na úrovni pracovního postupu, nikoli na úrovni aplikace logiky. Pokud chcete zkontrolovat historie triggerů mimo kontext historie spuštění, přečtěte si téma [Kontrola historie triggerů](#view-trigger-histories).

1. V nabídce Azure Portal v nabídce pracovního postupu vyberte **monitor**.

   V podokně **monitorování** se zobrazuje historie spuštění tohoto pracovního postupu.

   ![Snímek obrazovky znázorňující podokno monitorování a historii spuštění pracovního postupu](./media/create-stateful-stateless-workflows-azure-portal/find-run-history.png)

   > [!TIP]
   > Pokud se nezobrazuje poslední stav spuštění, na panelu nástrojů v podokně **monitorování** vyberte **aktualizovat**. Pro aktivační událost, která se přeskočila kvůli kritériím nesplnění nebo hledáním žádných dat, neproběhne žádné spuštění.

   | Stav spuštění | Description |
   |------------|-------------|
   | **Bylo přerušeno** | Spuštění bylo zastaveno nebo nebylo dokončeno z důvodu externích problémů, například výpadek systému nebo uplynulé předplatné Azure. |
   | **Stornován** | Běh se aktivoval a začal, ale přijal žádost o zrušení. |
   | **Neúspěšný** | Nejméně jedna akce v běhu se nezdařila. Pro zpracování této chyby nebyly nastaveny žádné následné akce v pracovním postupu. |
   | **Spuštěno** | Běh se aktivoval a probíhá, ale tento stav se může zobrazit i pro běh, který je omezený z důvodu [omezení akce](logic-apps-limits-and-config.md) nebo [aktuálního cenového plánu](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Tip**: Pokud nastavíte [diagnostické protokolování](monitor-logic-apps-log-analytics.md), můžete získat informace o všech událostech omezení, ke kterým dochází. |
   | **Úspěšný** | Spuštění proběhlo úspěšně. Pokud se některá akce nezdařila, došlo k selhání následné akce v pracovním postupu. |
   | **Vypršel časový limit** | Časový limit spuštění vypršel, protože aktuální doba překročila limit doby trvání běhu, který je řízen nastavením [ **uchování historie spuštění ve dnech**](logic-apps-limits-and-config.md#run-duration-retention-limits). Doba trvání běhu se počítá pomocí počátečního času spuštění a omezení doby trvání běhu v daném počátečním čase. <p><p>**Poznámka**: Pokud doba trvání běhu překročí také aktuální *limit uchování historie spuštění*, který je také řízen [nastavením **uchování historie spuštění v rámci dnů**](logic-apps-limits-and-config.md#run-duration-retention-limits), je spuštění vymazáno z historie spuštění podle každodenní úlohy čištění. Bez ohledu na to, jestli doba běhu vyprší nebo dokončí, se doba uchovávání vždycky vypočítá pomocí času spuštění a *aktuálního* limitu uchování. Pokud tedy omezíte dobu trvání spuštění v letadle, vyprší časový limit běhu. Běh ale buď zůstane, nebo se vymaže z historie spuštění na základě toho, jestli doba trvání běhu překročila limit uchování. |
   | **Čekající** | Běh se nezačal nebo je pozastaven, například kvůli dřívější instanci pracovního postupu, která je pořád spuštěná. |
   |||

1. Chcete-li zkontrolovat stav každého kroku spuštění, vyberte běh, který chcete zkontrolovat.

   Otevře se zobrazení Podrobnosti o spuštění a zobrazí se stav každého kroku v běhu.

   ![Snímek obrazovky zobrazující zobrazení podrobností o spuštění se stavem pro každý krok v pracovním postupu.](./media/create-stateful-stateless-workflows-azure-portal/review-run-details.png)

   Tady jsou možné stavy, které může každý krok pracovního postupu mít:

   | Stav akce | Ikona | Description |
   |---------------|------|-------------|
   | **Bylo přerušeno** | ![Ikona pro stav akce přerušeno][aborted-icon] | Akce se zastavila nebo nedokončila z důvodu externích problémů, například výpadek systému nebo uplynulé předplatné Azure. |
   | **Stornován** | ![Ikona pro stav akce zrušeno][cancelled-icon] | Akce byla spuštěna, ale přijala žádost o zrušení. |
   | **Neúspěšný** | ![Ikona pro stav akce "neúspěch"][failed-icon] | Akce se nezdařila. |
   | **Spuštěno** | ![Ikona pro spuštěný stav akce][running-icon] | Tato akce je aktuálně spuštěná. |
   | **Přeskočeno** | ![Ikona pro stav akce přeskočeno][skipped-icon] | Akce byla přeskočena, protože bezprostředně předchozí akce se nezdařila. Akce má `runAfter` podmínku, která vyžaduje, aby předchozí akce byla úspěšně dokončena předtím, než bude možné spustit aktuální akci. |
   | **Úspěšný** | ![Ikona stavu akce "úspěch"][succeeded-icon] | Akce byla úspěšná. |
   | **Úspěch s opakováním** | ![Ikona pro stav akce úspěšné a opakované pokusy][succeeded-with-retries-icon] | Akce byla úspěšná, ale jenom po jednom nebo několika opakovaných pokusech. Chcete-li zkontrolovat historii opakování, v zobrazení podrobností historie spuštění vyberte tuto akci, aby bylo možné zobrazit vstupy a výstupy. |
   | **Vypršel časový limit** | ![Ikona pro stav akce vypršel časový limit][timed-out-icon] | Akce byla zastavena z důvodu vypršení časového limitu zadaného nastavením této akce. |
   | **Čekající** | ![Ikona pro stav "čeká na akci"][waiting-icon] | Platí pro akci Webhooku, která čeká na příchozí požadavek od volajícího. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-azure-portal/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-azure-portal/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-azure-portal/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-azure-portal/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-azure-portal/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-azure-portal/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-azure-portal/waiting.png

1. Chcete-li zkontrolovat vstupy a výstupy pro určitý krok, vyberte tento krok.

   ![Snímek obrazovky zobrazující vstupy a výstupy ve vybrané akci "Odeslat e-mail".](./media/create-stateful-stateless-workflows-azure-portal/review-step-inputs-outputs.png)

1. Chcete-li dále zkontrolovat nezpracované vstupy a výstupy pro daný krok, vyberte možnost **Zobrazit nezpracované vstupy** nebo **Zobrazit nezpracované výstupy**.

<a name="view-trigger-histories"></a>

## <a name="review-trigger-histories"></a>Kontrola historie triggerů

U stavového pracovního postupu můžete zkontrolovat historii triggerů pro každé spuštění, včetně stavu triggeru společně se vstupy a výstupy, a to odděleně od [kontextu historie spuštění](#view-run-history). V Azure Portal se historie aktivačních událostí a spuštění zobrazí na úrovni pracovního postupu, nikoli na úrovni aplikace logiky. K vyhledání těchto historických dat použijte následující postup:

1. V nabídce Azure Portal v nabídce pracovní postup vyberte v části **vývojář** možnost **aktivovat historie**.

   Podokno **historie aktivačních událostí** zobrazuje historie aktivačních událostí pro spuštění vašeho pracovního postupu.

1. Pokud chcete zkontrolovat určitou historii triggerů, vyberte ID pro toto spuštění.

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Povolit nebo otevřít Application Insights po nasazení

Během provádění pracovního postupu vaše aplikace logiky generuje telemetrii spolu s dalšími událostmi. Pomocí této telemetrie získáte lepší přehled o tom, jak dobře je váš pracovní postup spuštěný a jak Logic Apps runtime funguje různým způsobem. Pracovní postup můžete monitorovat pomocí [Application Insights](../azure-monitor/app/app-insights-overview.md), která poskytuje telemetrii téměř v reálném čase (živé metriky). Tato funkce vám může pomáhat prozkoumat chyby a problémy s výkonem snadněji, když tato data použijete k diagnostice problémů, nastavení výstrah a sestavování grafů.

Pokud nastavení pro vytváření a nasazování vaší aplikace logiky podporují použití [Application Insights](../azure-monitor/app/app-insights-overview.md), můžete volitelně povolit diagnostické protokolování a trasování pro vaši aplikaci logiky. To můžete udělat buď při vytváření aplikace logiky v Azure Portal nebo po nasazení. Musíte mít instanci Application Insights, ale tento prostředek můžete vytvořit buď [předem](../azure-monitor/app/create-workspace-resource.md), při vytváření aplikace logiky nebo po nasazení.

Pokud chcete povolit Application Insights v nasazené aplikaci logiky nebo otevřete Application Insights řídicí panel, pokud už je povolený, postupujte podle těchto kroků:

1. V Azure Portal Najděte nasazenou aplikaci logiky.

1. V nabídce aplikace logiky v části **Nastavení** vyberte **Application Insights**.

1. Pokud Application Insights není povolená, vyberte v podokně **Application Insights** **zapnout Application Insights**. Po aktualizaci podokna klikněte v dolní části na **použít**.

   Pokud je povolená Application Insights, vyberte v podokně **Application Insights** možnost **Zobrazit Application Insights data**.

Po Application Insights se můžete podívat na různé metriky pro vaši aplikaci logiky. Další informace najdete v těchto tématech:

* [Azure Logic Apps běžící kdekoli – monitor s Application Insights – část 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Azure Logic Apps běžící kdekoli – monitor s Application Insights – část 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Povolit historii spuštění pro bezstavové pracovní postupy

Pokud chcete rychle ladit pracovní postup bez stavů, můžete pro tento pracovní postup povolit historii spuštění a po dokončení pak zakázat historii spuštění. Pomocí těchto kroků Azure Portal, nebo pokud pracujete v Visual Studio Code, přečtěte si téma [Vytvoření stavových a bezstavových pracovních postupů v Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless).

1. V [Azure Portal](https://portal.azure.com)vyhledejte a otevřete prostředek **Aplikace logiky (Preview)** .

1. V nabídce aplikace logiky v části **Nastavení** vyberte **Konfigurace**.

1. Na kartě **nastavení aplikace** vyberte možnost **nové nastavení aplikace**.

1. V podokně **Přidat nebo upravit nastavení aplikace** v poli **název** zadejte název možnosti této operace: 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. Do pole **hodnota** zadejte následující hodnotu: `WithStatelessRunHistory`

   Například:

   ![Snímek obrazovky, který zobrazuje prostředek Azure Portal a aplikace logiky (Preview) s názvem "konfigurace" > "nastavení nového aplikace" < otevře podokno přidat/upravit nastavení aplikace, a pracovní postupy. {yourWorkflowName}. Možnost OperationOptions je nastavena na hodnotu "WithStatelessRunHistory".](./media/create-stateful-stateless-workflows-azure-portal/stateless-operation-options-run-history.png)

1. Pro dokončení této úlohy vyberte **OK**. Na panelu nástrojů podokna **Konfigurace** vyberte **Uložit**.

1. Chcete-li po dokončení zakázat historii spuštění, buď nastavte `Workflows.{yourWorkflowName}.OperationOptions` vlastnost na `None` hodnotu nebo odstraňte vlastnost a její hodnotu.

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Odstranění položek z návrháře

Pokud chcete v pracovním postupu odstranit položku z návrháře, postupujte podle některého z těchto kroků:

* Vyberte položku, otevřete místní nabídku položky (Shift + F10) a vyberte **Odstranit**. Výběrem tlačítka **OK** potvrďte svůj úmysl.

* Vyberte položku a stiskněte klávesu DELETE. Výběrem tlačítka **OK** potvrďte svůj úmysl.

* Vyberte položku, aby se pro tuto položku otevřelo podokno podrobností. V pravém horním rohu podokna otevřete nabídku se třemi tečkami (**...**) a vyberte **Odstranit**. Výběrem tlačítka **OK** potvrďte svůj úmysl.

  ![Snímek obrazovky, který zobrazuje vybranou položku v Návrháři s otevřeným podoknem podrobností a tlačítkem se třemi tečkami a příkazem "odstranit".](./media/create-stateful-stateless-workflows-azure-portal/delete-item-from-designer.png)

  > [!TIP]
  > Pokud se nabídka se třemi tečkami nezobrazí, rozbalte okno prohlížeče dostatečně tak, aby se v podokně podrobností zobrazilo tlačítko se třemi tečkami (**...**) v pravém horním rohu.

<a name="troubleshoot"></a>

## <a name="troubleshoot-problems-and-errors"></a>Řešení problémů a chyb

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Ve výběru návrháře pro dříve vytvořené pracovní postupy chybí nové aktivační události a akce.

Azure Logic Apps Preview podporuje integrované akce pro operace Azure Functions, likvidní operace a operace XML, jako je **XML ověřování** a **transformace XML**. U dříve vytvořených aplikací logiky se ale tyto akce nemusí zobrazit v návrháři, abyste mohli vybrat, jestli aplikace logiky používá zastaralou verzi sady rozšíření `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

Pokud chcete tento problém vyřešit, postupujte podle těchto kroků a odstraňte zastaralou verzi, aby se sada rozšíření mohla automaticky aktualizovat na nejnovější verzi.

> [!NOTE]
> Toto konkrétní řešení se vztahuje pouze na prostředky **Aplikace logiky (Preview)** , které vytvoříte pomocí Azure Portal, nikoli aplikace logiky, které vytvoříte a nasazujete pomocí Visual Studio Code a rozšíření Azure Logic Apps (Preview). Viz téma [podporované triggery a akce chybí v návrháři v Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#missing-triggers-actions).

1. V Azure Portal zastavte aplikaci logiky.

   1. V nabídce aplikace logiky vyberte **Přehled**.

   1. Na panelu nástrojů v podokně **Přehled** vyberte **zastavit**.

1. V nabídce aplikace logiky v části **vývojové nástroje** vyberte **Rozšířené nástroje**.

1. V podokně **Pokročilé nástroje** vyberte **Přejít**, který otevře prostředí Kudu pro vaši aplikaci logiky.

1. Na panelu nástrojů Kudu otevřete nabídku **Konzola ladění** a vyberte **cmd**. 

   Otevře se okno konzoly, ve kterém můžete procházet složku sady prostředků pomocí příkazového řádku. Nebo můžete procházet adresářovou strukturu, která se zobrazí v okně konzoly.

1. Přejděte do následující složky, která obsahuje složky se správou verzí pro existující sadu prostředků:

   `...\home\data\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Odstraní složku verze existujícího balíčku. V okně konzoly můžete spustit tento příkaz, ve kterém nahradíte `{bundle-version}` stávající verzi:

   `rm -rf {bundle-version}`

   Příklad: `rm -rf 1.1.3`

   > [!TIP]
   > Pokud se zobrazí chyba, například "oprávnění odepřeno" nebo "soubor byl používán", aktualizujte stránku v prohlížeči a opakujte předchozí kroky, dokud nebude složka odstraněna.

1. V Azure Portal se vraťte na stránku **Přehled** vaší aplikace logiky a vyberte **restartovat**.

   Portál automaticky získá a použije nejnovější sadu prostředků.

## <a name="next-steps"></a>Další kroky

Rádi bychom od vás slyšeli o vašich zkušenostech s touto verzí Public Preview!

* V případě chyb nebo problémů [vytvořte problémy v GitHubu](https://github.com/Azure/logicapps/issues).
* Pro otázky, požadavky, komentáře a další zpětnou vazbu [použijte tento formulář zpětné vazby](https://aka.ms/lafeedback).
