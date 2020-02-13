---
title: Začínáme s ověřováním Azure AD pomocí Azure Portal | Microsoft Docs
description: Naučte se používat Azure Portal k přístupu k ověřování Azure Active Directory (Azure AD) ke využívání rozhraní API Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 4428187c985c1004c88f2ac20b0e5811803cce2a
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162765"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Začínáme s ověřováním Azure AD pomocí Azure Portal

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Projděte si nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-from-v2-to-v3.md) .

Naučte se používat Azure Portal k přístupu k ověřování Azure Active Directory (Azure AD) pro přístup k rozhraní API Azure Media Services.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure. Pokud účet nemáte, začněte s [bezplatnou zkušební verzí Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Účet Media Services. Další informace najdete v tématu [Vytvoření účtu Azure Media Services pomocí Azure Portal](media-services-portal-create-account.md).
- Ujistěte se, že máte [Přehled o přístupu k rozhraní API Azure Media Services pomocí Azure AD Authentication](media-services-use-aad-auth-to-access-ams-api.md). 

Pokud používáte ověřování Azure AD s Azure Media Services, máte dvě možnosti ověřování:

- **Ověřování uživatelů**. Ověřte osobu, která aplikaci používá k interakci s Media Servicesmi prostředky. Interaktivní aplikace by měla nejdřív vyzvat uživatele k zadání přihlašovacích údajů. Příkladem je aplikace konzoly pro správu používaná autorizovanými uživateli k monitorování úloh kódování nebo živého streamování. 
- **Ověřování instančního objektu**. Ověří službu. Aplikace, které běžně používají tuto metodu ověřování, jsou aplikace, které spouštějí služby démon, služby střední vrstvy nebo naplánované úlohy: webové aplikace, aplikace Function App, Logic Apps, rozhraní API nebo mikroslužba.

> [!IMPORTANT]
> V současné době Media Services podporuje model ověřování služby Azure Access Control. Access Control autorizaci ale bude zastaralá od 1. června 2018. Doporučujeme, abyste na model ověřování Azure AD migrovali co nejdříve.

## <a name="select-the-authentication-method"></a>Vyberte metodu ověřování.

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj účet Media Services.
2. Vyberte, jak se připojit k rozhraní Media Services API.

    ![Stránka vybrat způsob připojení](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Ověřování uživatelů

Pokud se chcete připojit k rozhraní Media Services API pomocí možnosti ověření uživatele, musí klientská aplikace požádat o token Azure AD s následujícími parametry:  

* Koncový bod tenanta Azure AD
* Identifikátor URI Media Services prostředku
* ID klienta aplikace Media Services (nativní) 
* Identifikátor URI přesměrování aplikace Media Services (nativní) 
* Identifikátor URI prostředku pro Media Services REST

Hodnoty pro tyto parametry můžete získat na stránce **Media Services API pomocí ověřování uživatelů** . 

![Stránka připojit k ověřování uživatelů](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Pokud se připojujete k rozhraní Media Services API pomocí Media Services sady Microsoft .NET SDK, jsou požadované hodnoty k dispozici jako součást sady SDK. Další informace najdete v tématu [použití ověřování Azure AD pro přístup k rozhraní Azure Media Services API pomocí .NET](media-services-dotnet-get-started-with-aad.md).

Pokud nepoužíváte sadu SDK klienta Media Services .NET, musíte ručně vytvořit žádost o token Azure AD pomocí výše popsaných parametrů. Další informace najdete v tématu [jak pomocí knihovny ověřování Azure AD získat token Azure AD](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Ověřování instančních objektů

Pokud se chcete připojit k rozhraní Media Services API pomocí možnosti instančního objektu, musí vaše aplikace střední vrstvy (webové rozhraní API nebo webová aplikace) požádat o token Azure AD s následujícími parametry:  

* Koncový bod tenanta Azure AD
* Identifikátor URI Media Services prostředku 
* Identifikátor URI prostředku pro Media Services REST
* Hodnoty aplikace Azure AD: **ID klienta** a **tajný klíč klienta**

Hodnoty pro tyto parametry můžete získat na stránce **připojit k Media Services rozhraní API s objektem služby** . Pomocí této stránky můžete vytvořit novou aplikaci Azure AD nebo vybrat některou z existujících. Po výběru aplikace Azure AD můžete získat ID klienta (ID aplikace) a vygenerovat hodnoty tajného klíče klienta (klíče). 

![Připojit k hlavní stránce služby](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

Když se otevře okno **instanční objekt** , vybere se první aplikace Azure AD, která splňuje následující kritéria:

- Jedná se o registrovanou aplikaci Azure AD.
- Má oprávnění přispěvatele nebo vlastníka na základě role Access Control na účtu.

Po vytvoření nebo výběru aplikace Azure AD můžete vytvořit a zkopírovat tajný klíč klienta (klíč) a ID klienta (ID aplikace). Pro získání přístupového tokenu v tomto scénáři se vyžaduje tajný kód klienta a ID klienta.

Pokud nemáte oprávnění k vytváření aplikací Azure AD ve vaší doméně, nezobrazí se ovládací prvky aplikace Azure AD v okně a zobrazí se zpráva s upozorněním.

Pokud se připojíte k rozhraní Media Services API pomocí sady Media Services .NET SDK, přečtěte si téma [použití ověřování Azure AD pro přístup k rozhraní api Azure Media Services pomocí .NET](media-services-dotnet-get-started-with-aad.md).

Pokud nepoužíváte sadu SDK klienta Media Services .NET, musíte ručně vytvořit žádost o token Azure AD pomocí výše popsaných parametrů. Další informace najdete v tématu [jak pomocí knihovny ověřování Azure AD získat token Azure AD](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Získat ID klienta a tajný klíč klienta

Po výběru existující aplikace služby Azure AD nebo výběru možnosti vytvořit nové se zobrazí následující tlačítka:

![Tlačítko pro správu oprávnění a tlačítko Spravovat aplikaci](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Pokud chcete otevřít okno aplikace Azure AD, klikněte na **Spravovat aplikaci**. V okně **Spravovat aplikaci** můžete získat ID klienta aplikace (ID aplikace). Pokud chcete vygenerovat tajný klíč klienta (klíč), vyberte **klíče**.

![Možnost spravovat klávesy v okně aplikace](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Správa oprávnění a aplikace

Po výběru aplikace Azure AD můžete spravovat aplikaci a oprávnění. Pokud chcete nastavit aplikaci Azure AD pro přístup k jiným aplikacím, klikněte na **Spravovat oprávnění**. Pro úlohy správy, jako je například změna klíčů a adres URL odpovědí, nebo úprava manifestu aplikace, klikněte na **Spravovat aplikaci**.

### <a name="edit-the-apps-settings-or-manifest"></a>Úprava nastavení nebo manifestu aplikace

Pokud chcete upravit nastavení nebo manifest aplikace, klikněte na **Spravovat aplikaci**.

![Spravovat stránku aplikace](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>Další kroky

Začněte s [nahráváním souborů na svůj účet](media-services-portal-upload-files.md).
