---
title: Rozhraní API pro odeslání partnerského centra pro zprovoznění aplikací Azure na komerčním webu Microsoft Marketplace
description: Seznamte se s požadavky na používání rozhraní API pro odesílání v partnerském centru pro aplikace Azure na komerčním webu Marketplace v partnerském centru Microsoftu.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: ed8f5b8567e5808ebccfdc2a6d2d3b5d530da5cc
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107884007"
---
# <a name="partner-center-submission-api-to-onboard-azure-apps-in-partner-center"></a>Rozhraní API pro odeslání partnerského centra pro zprovoznění aplikací Azure v partnerském centru

*Rozhraní API pro odeslání prostřednictvím služby partner Center* slouží k programovému dotazování, vytváření odesílání pro a publikování nabídek Azure.  Toto rozhraní API je užitečné v případě, že váš účet spravuje mnoho nabídek a chcete automatizovat a optimalizovat proces odeslání pro tyto nabídky.

## <a name="api-prerequisites"></a>Požadavky API

K dispozici je několik programových prostředků, které potřebujete k používání rozhraní API partnerského centra pro produkty Azure: 

- aplikace Azure Active Directory.
- přístupový token pro Azure Active Directory (Azure AD).

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Krok 1: dokončení požadavků na použití rozhraní API pro odeslání partnerského centra

Než začnete psát kód pro volání rozhraní API pro odeslání partnerského centra, ujistěte se, že jste dokončili následující požadavky.

- Vy (nebo vaše organizace) musí mít adresář služby Azure AD a musíte mít oprávnění [globálního správce](../../active-directory/roles/permissions-reference.md) pro tento adresář. Pokud už používáte Microsoft 365 nebo jiné firemní služby od Microsoftu, už máte adresář Azure AD. V opačném případě můžete [vytvořit novou službu Azure AD v partnerském centru](/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) bez dalších poplatků.

- Musíte [přidružit aplikaci Azure AD k účtu partnerského centra](/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) a získat ID TENANTA, ID klienta a klíč. Tyto hodnoty budete potřebovat k získání přístupového tokenu Azure AD, který použijete v volání rozhraní API pro odeslání Microsoft Store.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Jak přidružit aplikaci Azure AD k účtu partnerského centra

Pokud chcete použít rozhraní API pro odesílání Microsoft Store, musíte aplikaci Azure AD přidružit k účtu partnerského centra, načíst ID tenanta a ID klienta pro aplikaci a vygenerovat klíč. Aplikace Azure AD představuje aplikaci nebo službu, ze které chcete zavolat rozhraní API pro odeslání partnerského centra. K získání přístupového tokenu Azure AD, který předáte do rozhraní API, potřebujete ID tenanta, ID klienta a klíč.

>[!Note]
>Tuto úlohu je třeba provést pouze jednou. Až budete mít ID tenanta, ID klienta a klíč, můžete je znovu použít, kdykoli budete potřebovat vytvořit nový přístupový token Azure AD.

1. V partnerském centru [přidružte účet partnerského centra vaší organizace k adresáři Azure AD vaší organizace](/windows/uwp/publish/associate-azure-ad-with-partner-center).
1. Potom na stránce **Uživatelé** v části **Nastavení účtu** v partnerském centru [přidejte aplikaci Azure AD](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) , která představuje aplikaci nebo službu, kterou budete používat pro přístup k odeslání účtu partnerského centra. Ujistěte se, že tuto aplikaci přiřadíte roli **správce** . Pokud aplikace ještě v adresáři Azure AD neexistuje, můžete [vytvořit novou aplikaci Azure AD v partnerském centru](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. Vraťte se na stránku **Uživatelé** , klikněte na název vaší aplikace Azure AD, přejděte do nastavení aplikace a zkopírujte **ID TENANTA** a hodnoty **ID klienta** .
1. Klikněte na **Přidat nový klíč**. Na následující obrazovce Zkopírujte hodnotu **klíče** . Po opuštění této stránky nebudete moct znovu získat přístup k těmto informacím. Další informace najdete v tématu [Správa klíčů pro aplikaci Azure AD](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Krok 2: získání přístupového tokenu Azure AD

Před voláním kterékoli z metod v rozhraní API pro odeslání z partnerského centra musíte nejdřív získat přístupový token Azure AD, který předáte do **autorizační** hlavičky každé metody v rozhraní API. Po získání přístupového tokenu máte 60 minut, než ho začnete používat. Po vypršení platnosti tokenu můžete token aktualizovat, abyste ho mohli dál používat v budoucích voláních rozhraní API.

Pokud chcete získat přístupový token, postupujte podle pokynů v tématu [volání služby Service to Service Calls pomocí pověření klienta](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) k odeslání `HTTP POST` do `https://login.microsoftonline.com/<tenant_id>/oauth2/token` koncového bodu. Tady je ukázkový požadavek:

JSONCopy
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

V případě *tenant_id* hodnoty v `POST URI` parametrech a *CLIENT_ID* a *client_secret* zadejte ID tenanta, ID klienta a klíč vaší aplikace, které jste získali z partnerského centra v předchozí části. Pro parametr *prostředku* je nutné zadat `https://api.partner.microsoft.com` .

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Krok 3: použití rozhraní API pro odeslání Microsoft Store

Po získání přístupového tokenu Azure AD můžete volat metody v rozhraní API pro odesílání v partnerském centru. Pokud chcete vytvořit nebo aktualizovat odesílání, obvykle v určitém pořadí zavoláte do rozhraní API pro odesílání v partnerském centru více metod. Informace o jednotlivých scénářích a syntaxi jednotlivých metod najdete v tématu rozhraní API pro přijímání ingestování.

https://apidocs.microsoft.com/services/partneringestion/

## <a name="next-steps"></a>Další kroky

* [Vytvoření technického prostředku kontejneru Azure](../azure-container-technical-assets.md)
* [Vytvoření nabídky kontejnerů Azure](../azure-container-offer-setup.md)