---
title: Nelze přidat vlastní doménu pomocí Key Vaultho certifikátu.
titleSuffix: Azure API Management
description: Naučte se řešit potíže, při kterých nemůžete do Azure API Management přidat vlastní doménu pomocí certifikátu trezoru klíčů.
services: api-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: a09c15466a4a9f62b2696b087cb7ab23cc767379
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "75430574"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>Nepovedlo se aktualizovat názvy hostitelů služby API Management.

Tento článek popisuje chybu "nepovedlo se aktualizovat názvy hostitelů služby API Management), se kterými se můžete setkat při přidávání vlastní domény pro službu Azure API Management. Tento článek popisuje kroky pro řešení potíží, které vám pomůžou tento problém vyřešit.

## <a name="symptoms"></a>Příznaky

Při pokusu o přidání vlastní domény pro službu API Management pomocí certifikátu z Azure Key Vault se zobrazí následující chybová zpráva:

- Nepovedlo se aktualizovat názvy hostitelů služby API Management. Požadavek na prostředek https://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0 se nezdařil s příznakem StatusCode: zakázáno pro RequestId:. Zpráva o výjimce: operace vrátila neplatný stavový kód zakázáno.

## <a name="cause"></a>Příčina

Služba API Management nemá oprávnění pro přístup k trezoru klíčů, který se pokoušíte použít pro vlastní doménu.

## <a name="solution"></a>Řešení

Pokud chcete tento problém vyřešit, postupujte následovně:

1. Otevřete [Azure Portal](Https://portal.azure.com), vyberte svou instanci API Management a pak vyberte **spravované identity**. Ujistěte se, že možnost **registrovat s Azure Active Directory** je nastavená na **hodnotu Ano**. 
    ![Registrace ve službě Azure Active Director](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. V Azure Portal otevřete službu **trezory klíčů** a vyberte Trezor klíčů, který se pokoušíte použít pro vlastní doménu.
1. Vyberte **zásady přístupu** a ověřte, jestli existuje instanční objekt, který odpovídá názvu instance služby API Management. V takovém případě vyberte instanční objekt a ujistěte se, že má oprávnění **získat** uvedené v části **oprávnění tajného** kódu.  
    ![Přidání zásad přístupu pro instanční objekt](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png)
1. Pokud služba API Management není v seznamu, vyberte **Přidat zásadu přístupu** a pak vytvořte následující zásady přístupu:
    - **Konfigurovat ze šablony**: žádné
    - **Vyberte objekt zabezpečení**: vyhledejte název služby API Management a vyberte ji ze seznamu.
    - **Oprávnění klíče**: žádné
    - **Oprávnění k tajným klíčům**: získat
    - **Oprávnění certifikátu**: žádné
1. Vyberte **OK** a vytvořte zásady přístupu.
1. Kliknutím na **Uložit** uložte změny.

Ověřte, zda je problém vyřešen. Chcete-li to provést, zkuste vytvořit vlastní doménu ve službě API Management pomocí certifikátu Key Vault.

## <a name="next-steps"></a>Další kroky
Další informace o službě API Management Service:

- Podívejte se na další [videa](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o API Management.
* Další způsoby zabezpečení back-endové služby najdete v tématu [vzájemné ověřování certifikátů](api-management-howto-mutual-certificates.md).

* [Vytvořte instanci služby API Management](get-started-create-service-instance.md).
* [Spravujte své první rozhraní API](import-and-publish.md).