---
title: Začínáme s ověřováním Azure AD pomocí portálu Azure | Microsoft Docs
description: Naučte se používat portál Azure pro přístup k ověřování Azure Active Directory (Azure AD) využívají rozhraní API služby Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 6267dd8dca4c932d4a4d96b34a8eaa26f6a59c20
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Začínáme s ověřováním Azure AD pomocí portálu Azure

Naučte se používat portál Azure pro přístup k ověřování Azure Active Directory (Azure AD) pro přístup k rozhraní API služby Azure Media Services.

## <a name="prerequisites"></a>Požadavky

- Účet Azure. Pokud nemáte účet, začínat [bezplatné zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Účet Media Services. Další informace najdete v tématu [vytvoření účtu Azure Media Services pomocí portálu Azure](media-services-portal-create-account.md).
- Ujistěte se, můžete zkontrolovat [přístup k Azure Media Services API s Azure AD authentication přehled](media-services-use-aad-auth-to-access-ams-api.md). 

Při použití ověřování Azure AD pomocí služby Azure Media Services, máte dvě možnosti ověřování:

- **Ověření uživatele**. Ověření osoba, která používá aplikace komunikovat s prostředky Media Services. Interaktivní aplikace by měla nejprve vyzvou uživatele k zadání přihlašovacích údajů. Příkladem je aplikace konzoly správy Autorizovaní uživatelé používají ke sledování úloh kódování nebo živé streamování. 
- **Objekt zabezpečení ověřování služby**. Ověření služby. Aplikace, které běžně používají tuto metodu ověřování, jsou aplikace, které běží služby démon, střední vrstvy služby nebo naplánované úlohy: webové aplikace, funkce aplikace, aplikace logiky, rozhraní API nebo mikroslužby.

> [!IMPORTANT]
> V současné době Media Services podporuje model řízení přístupu Azure služby ověřování. Řízení přístupu autorizace však bude na 1. června 2018 zastaralá. Doporučujeme, abyste na model ověřování Azure AD migrovali co nejdříve.

## <a name="select-the-authentication-method"></a>Vyberte metodu ověřování

1. V [portál Azure](https://portal.azure.com/), vyberte svůj účet Media Services.
2. Vyberte, jak se připojit k rozhraní API služby Media Services.

    ![Vyberte stránku metoda připojení](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Ověřování uživatelů

Pro připojení k rozhraní API služby Media Services pomocí možnosti ověřování uživatele, klientská aplikace potřebuje k vyžádání tokenu Azure AD, který má následující parametry:  

* Koncový bod tenant Azure AD
* Identifikátor URI prostředku Media Services
* ID klienta aplikace Media Services (nativní) 
* Identifikátor URI pro přesměrování aplikace Media Services (nativní) 
* Identifikátor URI pro REST Media Services

Hodnoty pro tyto parametry můžete získat **Media Services API s ověřováním uživatele** stránky. 

![Připojení k stránce ověřování uživatele](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Pokud se připojit k rozhraní API služby Media Services pomocí .NET SDK služby Media Services Microsoft požadované hodnoty jsou k dispozici jako součást sady SDK. Další informace najdete v tématu [ověřování pomocí služby Azure AD pro přístup k rozhraní API Azure Media Services pomocí rozhraní .NET](media-services-dotnet-get-started-with-aad.md).

Pokud nepoužíváte klienta Media Services .NET SDK, musíte ručně vytvořit požadavek tokenu Azure AD pomocí parametrů už jsme probírali výše. Další informace najdete v tématu [jak používat knihovnu ověřování služby Azure AD k získání tokenu Azure AD](../../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Ověřování instančních objektů

Vaše aplikace střední vrstvy (webového rozhraní API nebo webové aplikace) pro připojení k rozhraní API služby Media Services pomocí možnosti hlavní služby, musí žádosti o token Azure AD, který má následující parametry:  

* Koncový bod tenant Azure AD
* Identifikátor URI prostředku Media Services 
* Identifikátor URI pro REST Media Services
* Hodnoty aplikace Azure AD: **ID klienta** a **tajný klíč klienta**

Hodnoty pro tyto parametry můžete získat **připojit k rozhraní API služby média pomocí objektu služby** stránky. Na této stránce můžete vytvořit novou aplikaci Azure AD nebo vybrat stávající. Když vyberete aplikaci Azure AD, můžete získat ID klienta (ID aplikace) a generování hodnot tajný klíč (klíč) klienta. 

![Připojení k hlavní stránce služby](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

Když **instanční objekt** otevře se okno, je vybrána první aplikace Azure AD, která splňuje následující kritéria:

- Je registrovaný aplikaci Azure AD.
- Na účet má oprávnění přispěvatele nebo Owner Role-Based řízení přístupu.

Po vytvoření nebo vyberte aplikaci Azure AD, můžete vytvořit a zkopírovat tajný klíč klienta (klíč) a ID klienta (ID aplikace). K získání přístupu tokenu v tomto scénáři se vyžadují tajný klíč klienta a ID klienta.

Pokud nemáte oprávnění k vytvoření aplikace Azure AD ve vaší doméně, nejsou zobrazeny v okně Ovládací prvky aplikace Azure AD a zobrazí zpráva s upozorněním.

Pokud se připojit k rozhraní API služby Media Services pomocí sady Media Services .NET SDK najdete v části [ověřování pomocí služby Azure AD pro přístup k rozhraní API Azure Media Services pomocí rozhraní .NET](media-services-dotnet-get-started-with-aad.md).

Pokud nepoužíváte klienta Media Services .NET SDK, musíte ručně vytvořit požadavek tokenu Azure AD pomocí parametrů už jsme probírali výše. Další informace najdete v tématu [jak používat knihovnu ověřování služby Azure AD k získání tokenu Azure AD](../../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Získání ID klienta a tajný klíč klienta

Jakmile vybrat existující aplikaci Azure AD nebo vybrat možnost vytvořit novou, se zobrazí následující tlačítka:

![Správa oprávnění tlačítka a Správa aplikací](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Chcete-li otevřít okno aplikace Azure AD, klikněte na tlačítko **správě aplikace**. Na **správě aplikace** okně můžete získat ID klienta aplikace (ID aplikace). Chcete-li vygenerovat tajný klíč klienta (klíč), vyberte **klíče**.

![Spravovat aplikace okna klíče možnost](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Správa oprávnění a aplikace

Jakmile vyberete aplikace Azure AD, můžete spravovat aplikace a oprávnění. Chcete-li nastavit aplikaci Azure AD pro přístup k jiné aplikace, klikněte na tlačítko **spravovat oprávnění**. Pro úlohy správy, jako je například změna klíčů a adresy URL odpovědí, nebo upravit manifestu aplikace, klikněte na tlačítko **správě aplikace**.

### <a name="edit-the-apps-settings-or-manifest"></a>Upravit nastavení aplikace nebo manifest

Chcete-li upravit nastavení nebo manifestu aplikace, klikněte na tlačítko **správě aplikace**.

![Spravovat stránky aplikace](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>Další postup

Začínáme s [nahrávání souborů do účtu](media-services-portal-upload-files.md).
