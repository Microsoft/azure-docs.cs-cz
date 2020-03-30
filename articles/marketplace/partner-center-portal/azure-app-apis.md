---
title: Připojení rozhraní API pro aplikace Azure na komerčním trhu
description: Požadavky rozhraní API pro aplikace Azure na komerčním trhu v Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: cc4d56058ce3985ec3a1d9124ef4ec73ff6be1a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279755"
---
# <a name="api-for-onboarding-azure-apps-in-partner-center"></a>ROZHRANÍ API pro připojení aplikací Azure v Partnerském centru

Pomocí *rozhraní API pro odesílání v Partnerském centru* můžete programově dotazovat, vytvářet odeslání a publikovat nabídky Azure.  Toto rozhraní API je užitečné, pokud váš účet spravuje mnoho nabídek a chcete automatizovat a optimalizovat proces odesílání pro tyto nabídky.

## <a name="api-prerequisites"></a>Požadavky rozhraní API

K použití rozhraní API partnerského centra pro produkty Azure potřebujete několik programových prostředků: 

- aplikace Azure Active Directory.
- přístupový token služby Azure Active Directory (Azure AD).

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Krok 1: Dokončení předpokladů pro použití rozhraní API pro odesílání v Partnerském centru

Než začnete psát kód pro volání rozhraní API pro odeslání v Partnerském centru, ujistěte se, že jste dokončili následující požadavky.

- Vy (nebo vaše organizace) musíte mít adresář Azure AD a musíte mít [oprávnění globálního správce](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) pro adresář. Pokud už používáte Office 365 nebo jiné obchodní služby od Microsoftu, už máte adresář Azure AD. V opačném případě můžete [vytvořit nový Azure AD v Centru partnerů](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) bez dalších poplatků.

- Aplikaci Azure AD musíte [přidružit k účtu Partnerského centra](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) a získat ID klienta, ID klienta a klíč. Tyto hodnoty potřebujete k získání přístupového tokenu Azure AD, který budete používat při volání rozhraní API pro odeslání Microsoft Store.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Jak přidružit aplikaci Azure AD k účtu Partnerského centra

Chcete-li použít rozhraní API pro odeslání microsoft store, musíte přidružit aplikaci Azure AD k účtu Partner ského centra, načíst ID klienta a ID klienta pro aplikaci a vygenerovat klíč. Aplikace Azure AD představuje aplikaci nebo službu, ze které chcete volat rozhraní API pro odeslání partnerského centra. Potřebujete ID klienta, ID klienta a klíč k získání přístupového tokenu Azure AD, který předáte do rozhraní API.

>[!Note]
>Tento úkol je třeba provést pouze jednou. Po budete mít ID klienta, ID klienta a klíč, můžete je znovu použít kdykoli v případě, že potřebujete vytvořit nový přístupový token Azure AD.

1. V Partnerském centru [přidružte účet Partnerského centra vaší organizace k adresáři Azure AD vaší organizace](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center).
1. Dále na stránce **Uživatelé** v části **Nastavení účtu** v Centru partnerů [přidejte aplikaci Azure AD,](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) která představuje aplikaci nebo službu, kterou použijete pro přístup k odeslání pro váš účet Partnerského centra. Ujistěte se, že jste této aplikaci přiřadili roli **Správce.** Pokud aplikace ještě neexistuje ve vašem adresáři Azure AD, můžete [vytvořit novou aplikaci Azure AD v Centru partnerů](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. Vraťte se na stránku **Uživatelé,** kliknutím na název aplikace Azure AD přejděte na nastavení aplikace a zkopírujte hodnoty **ID klienta** a **ID klienta.**
1. Klepněte na tlačítko **Přidat novou klávesu**. Na následující obrazovce zkopírujte hodnotu **Klíč.** Po opuštění této stránky nebudete mít přístup k těmto informacím znovu. Další informace najdete [v tématu Správa klíčů pro aplikaci Azure AD](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Krok 2: Získání přístupového tokenu Azure AD

Před voláním některé z metod v rozhraní API pro odesílání partnerského centra musíte nejprve získat přístupový token Azure AD, který předáte do hlavičky **autorizace** každé metody v rozhraní API. Po získání přístupového tokenu máte 60 minut na jeho použití před vypršením jeho platnosti. Po vypršení platnosti tokenu můžete aktualizovat token, abyste ho mohli dál používat v budoucích voláních rozhraní API.

Chcete-li získat přístupový token, postupujte podle pokynů v `HTTP POST` service `https://login.microsoftonline.com/<tenant_id>/oauth2/token` volání pomocí [pověření klienta](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) k odeslání koncového bodu. Zde je ukázkový požadavek:

JSONCopy (JSON)
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

Pro *tenant_id* hodnotu `POST URI` v a *a client_id* a *client_secret* parametry, zadejte ID klienta, ID klienta a klíč pro vaši aplikaci, kterou jste načetli z Centra partnerů v předchozí části. Pro parametr *prostředku* je `https://api.partner.microsoft.com`nutné zadat .

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Krok 3: Použití rozhraní API pro odeslání microsoft store

Po vytvoření přístupového tokenu Azure AD můžete volat metody v rozhraní API pro odesílání v Partnerském centru. Chcete-li vytvořit nebo aktualizovat odeslání, obvykle voláte více metod v rozhraní API pro odeslání partnerského centra v určitém pořadí. Informace o jednotlivých scénářích a syntaxi každé metody naleznete v tématu Ingestion API naparování.

https://apidocs.microsoft.com/services/partneringestion/
