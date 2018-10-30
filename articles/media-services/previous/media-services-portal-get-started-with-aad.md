---
title: Začínáme s ověřováním Azure AD pomocí portálu Azure | Dokumentace Microsoftu
description: Zjistěte, jak přistupovat k ověřování Azure Active Directory (Azure AD) Chcete-li využívají rozhraní API služby Azure Media Services pomocí webu Azure portal.
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
ms.date: 10/29/2018
ms.author: juliako
ms.openlocfilehash: bae134d30bd175656e4da9121bca8dbfcfe7fcdd
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50229455"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Začínáme s ověřováním Azure AD pomocí webu Azure portal

Zjistěte, jak přistupovat k ověřování Azure Active Directory (Azure AD) pro přístup k rozhraní API služby Azure Media Services pomocí webu Azure portal.

## <a name="prerequisites"></a>Požadavky

- Účet Azure. Pokud účet nemáte, začněte tématem [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Účet Media Services. Další informace najdete v tématu [vytvoření účtu Azure Media Services pomocí webu Azure portal](media-services-portal-create-account.md).
- Ujistěte se, že se podíváte [přístup k API služby Azure Media pomocí ověřování Azure AD – přehled](media-services-use-aad-auth-to-access-ams-api.md). 

Pokud používáte ověřování Azure AD pomocí služby Azure Media Services, máte dvě možnosti ověřování:

- **Ověřování uživatelů**. Ověření osoba, která používá aplikace k interakci s prostředky služby Media Services. Interaktivní aplikace by měly nejprve vyzvou uživatele pro přihlašovací údaje. Příkladem je aplikace konzoly správy Autorizovaní uživatelé používají ke sledování úloh kódování nebo živého streamování. 
- **Ověřování instančních objektů**. Ověření služby. Aplikace, které se běžně používají tuto metodu ověřování jsou aplikace, na kterých běží služby démonů, střední vrstvy služby nebo podle plánu: webové aplikace, aplikace function App, logic apps, rozhraní API nebo mikroslužby.

> [!IMPORTANT]
> V současné době služba Media Services podporuje model ověřování Azure Access Control service. Ale autorizaci na řízení přístupu se přestanou používat 1. června 2018. Doporučujeme, abyste na model ověřování Azure AD migrovali co nejdříve.

## <a name="select-the-authentication-method"></a>Vyberte metodu ověřování

1. V [webu Azure portal](https://portal.azure.com/), vyberte svůj účet Media Services.
2. Vyberte, jak se připojit k rozhraní API služby Media Services.

    ![Vyberte stránku připojení – metoda](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Ověřování uživatelů

Pro připojení k rozhraní API služby Media Services pomocí možnosti ověřování uživatele, klientská aplikace potřebuje požádat o token Azure AD, který má následující parametry:  

* Koncový bod tenant Azure AD
* Identifikátor URI prostředku služby Media Services
* ID klienta aplikace služby Media Services (nativní) 
* Identifikátor URI pro přesměrování aplikace služby Media Services (nativní) 
* Identifikátor URI pro REST Media Services

Hodnoty pro tyto parametry můžete získat **rozhraní API služby Media Services pomocí ověření uživatele** stránky. 

![Napojení na stránce ověřování uživatele](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Pokud připojíte k rozhraní API služby Media Services pomocí sady Media Services Microsoft .NET SDK, povinné hodnoty jsou k dispozici jako součást sady SDK. Další informace najdete v tématu [ověřování pomocí služby Azure AD pro přístup k rozhraní API Azure Media Services s .NET](media-services-dotnet-get-started-with-aad.md).

Pokud nepoužíváte klienta Media Services .NET SDK, musíte ručně vytvořit požadavek tokenu Azure AD pomocí parametrů jsme probírali výše. Další informace najdete v tématu [jak používat Azure AD Authentication Library pro získání tokenu Azure AD](../../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Ověřování instančních objektů

Pro připojení k rozhraní API služby Media Services pomocí možnosti instančního objektu služby, aplikace střední vrstvy (webového rozhraní API nebo webové aplikace) potřebuje požádat o token Azure AD, který má následující parametry:  

* Koncový bod tenant Azure AD
* Identifikátor URI prostředku služby Media Services 
* Identifikátor URI pro REST Media Services
* Hodnoty aplikace Azure AD: **ID klienta** a **tajný klíč klienta**

Hodnoty pro tyto parametry můžete získat **připojit k rozhraní API služby Media Services pomocí instančního objektu** stránky. Tato stránka slouží k vytvoření nové aplikace Azure AD nebo vyberte existující. Jakmile vyberete aplikace Azure AD, můžete získat ID klienta (ID aplikace) a vygenerovat tajný klíč (klíč) hodnot klienta. 

![Připojení s hlavní stránkou služby](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

Když **instanční objekt služby** otevře se okno, je vybrána první aplikaci Azure AD, který splňuje následující kritéria:

- Je registrované aplikaci Azure AD.
- Má oprávnění Přispěvatel nebo Owner Role-Based řízení přístupu u účtu.

Po vytvoření nebo výběru aplikace Azure AD, můžete vytvořit a zkopírovat tajný klíč klienta (klíč) a ID klienta (ID aplikace). Tajný klíč klienta a ID klienta je potřeba získat přístup k tokenu v tomto scénáři.

Pokud nemáte oprávnění k vytvoření aplikace Azure AD ve vaší doméně, nejsou zobrazeny ovládací prvky aplikací Azure AD okně a zobrazí se zpráva upozornění.

Pokud připojíte k rozhraní API služby Media Services pomocí sady Media Services .NET SDK, přečtěte si téma [ověřování pomocí služby Azure AD pro přístup k rozhraní API Azure Media Services s .NET](media-services-dotnet-get-started-with-aad.md).

Pokud nepoužíváte klienta Media Services .NET SDK, musíte ručně vytvořit požadavek tokenu Azure AD pomocí parametrů jsme probírali výše. Další informace najdete v tématu [jak používat Azure AD Authentication Library pro získání tokenu Azure AD](../../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Získání ID klienta a tajný kód klienta

Po vybrat existující aplikaci Azure AD nebo vybrat možnost vytvořit novou, zobrazí se následující tlačítka:

![Spravovat oprávnění a spravovat aplikace](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Otevře se okno aplikace Azure AD, klikněte na tlačítko **spravovat aplikaci**. Na **spravovat aplikaci** okně můžete získat ID klienta aplikace (ID aplikace). Chcete-li vygenerovat tajný klíč klienta (klíč), vyberte **klíče**.

![Správa možností okno klíče aplikace](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Spravovat oprávnění a aplikace

Jakmile vyberete aplikace Azure AD, můžete spravovat aplikace a oprávnění. Nastavení aplikace Azure AD pro přístup k aplikacím, klikněte na tlačítko **spravovat oprávnění**. Pro úlohy správy, jako je například změna klíčů a adres URL odpovědí nebo úprava manifestu aplikace, klikněte na tlačítko **spravovat aplikaci**.

### <a name="edit-the-apps-settings-or-manifest"></a>Upravit nastavení aplikace nebo manifestu

Chcete-li upravit nastavení nebo manifestu aplikace, klikněte na tlačítko **spravovat aplikaci**.

![Spravovat stránky aplikace](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>Další postup

Začínáme s [nahrávání souborů do účtu](media-services-portal-upload-files.md).
