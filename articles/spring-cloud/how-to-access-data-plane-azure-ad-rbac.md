---
title: Přístup ke konfiguračnímu serveru a registru služby
titleSuffix: Azure Spring Cloud
description: Jak získat přístup k koncovým bodům serveru konfigurace a služby registru pomocí Azure Active Directory řízení přístupu na základě role.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 23e24e562ea6fa10eee82c54c9ab2a701dd10351
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169880"
---
# <a name="access-config-server-and-service-registry"></a>Přístup ke konfiguračnímu serveru a registru služby

V tomto článku se dozvíte, jak získat přístup k jarnímu konfiguračnímu serveru a k registru jarní cloudové služby spravovanému pomocí Azure jaře cloudu pomocí služby Azure Active Directory (Azure AD) řízení přístupu na základě role (RBAC).

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>Přiřazení role k uživateli/skupině, MSI nebo instančnímu objektu Azure AD

Pokud chcete používat Azure AD a RBAC, musíte roli *čtečky dat cloudu Azure jaře* přiřadit uživateli, skupině nebo instančnímu objektu následujícím postupem:

1. Přejít na stránku Přehled služby instance služby.

2. Kliknutím na **Access Control (IAM)** otevřete okno řízení přístupu.

3. Klikněte na tlačítko **Přidat** a **přidejte přiřazení rolí** (pro přidání může být vyžadováno autorizaci).

4. Najděte a vyberte v části **role** možnost *čtecí modul cloudových dat Azure ve jaře* .
5. Přiřaďte přístup k `User, group, or service principal` nebo `User assigned managed identity` podle typu uživatele. Vyhledejte a vyberte možnost uživatel.  
6. Klikněte na `Save`.

   ![přiřazení – role](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-config-server-and-service-registry-endpoints"></a>Přístup ke konfiguračnímu serveru a koncovým bodům registru služby

Po přiřazení role čtečky dat cloudu Azure je možné, že zákazníci budou mít přístup k serveru jarního cloudu a koncovým bodům registru služby jarní Cloud Service. Použijte následující postupy:

1. Získání přístupového tokenu Jakmile se uživateli Azure AD přiřadí role čtečky dat cloudu Azure jaře, můžou se pomocí následujících příkazů přihlásit do Azure CLI s uživatelským, instančním objektem nebo spravovanou identitou a získat přístupový token. Podrobnosti najdete v tématu [ověření Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). 

    ```azurecli
    az login
    az account get-access-token
    ```
2. Vytvořte koncový bod. Podporujeme výchozí koncové body pro jarní cloudový konfigurační server a registr jarní cloudové služby, které spravuje Azure jaře Cloud. Další informace najdete v tématu [koncové body připravené pro produkční](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)prostředí. Zákazníci můžou také získat úplný seznam podporovaných koncových bodů pro jarní cloudový konfigurační server a registr jarní cloudové služby, který spravuje Azure jaře Cloud, a to díky přístupu k koncovým bodům:

    * *https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/*
    * *https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/* 

3. Přístup k složenému koncovému bodu pomocí přístupového tokenu. Pokud chcete zajistit autorizaci, vložte přístupový token do hlavičky.  Podporovaná je jenom metoda GET.

    Můžete například získat přístup ke koncovému bodu, jako *https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/health* je třeba zobrazení stavu Eureka.

    Pokud je odpověď *401 neoprávněná*, zkontrolujte, jestli je role úspěšně přiřazená.  Bude to trvat několik minut, než se role projeví, nebo ověřit, že platnost přístupového tokenu nevypršela.

## <a name="next-steps"></a>Další kroky
* [Ověření Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [Koncové body připravené pro produkci](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>Viz také
* [Vytváření rolí a oprávnění](how-to-permissions.md)
