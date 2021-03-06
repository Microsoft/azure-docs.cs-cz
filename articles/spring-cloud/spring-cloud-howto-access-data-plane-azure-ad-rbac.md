---
title: Přístup k rovině dat pomocí Azure AD, RBAC
description: Jak získat přístup k rovině dat pomocí Azure Active Directory řízení přístupu na základě role.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 2909d40fbc7cb5b310ea17f17a6e683ce47638ce
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102220118"
---
# <a name="access-the-data-plane-with-azure-active-directory-and-role-based-access-control"></a>Přístup k rovině dat pomocí Azure Active Directory a Access Control na základě rolí

V tomto článku se dozvíte, jak získat přístup k koncovým bodům konfiguračního serveru cloudu Azure a službě Service Registry pomocí Azure Active Directory (AAD) řízení přístupu na základě role (RBAC).

## <a name="assign-role-to-aad-usergroup-msi-or-service-principal"></a>Přiřadit roli uživateli/skupině AAD, MSI nebo instančnímu objektu

Pokud chcete používat AAD a RBAC, musíte roli *čtečky dat cloudu Azure jaře* přiřadit uživateli, skupině nebo instančnímu objektu následujícím postupem:

1. Přejít na stránku Přehled služby instance služby.

2. Kliknutím na **Access Control (IAM)** otevřete okno řízení přístupu.

3. Klikněte na tlačítko **Přidat** a **přidejte přiřazení rolí** (pro přidání může být vyžadováno autorizaci).

4. Najděte a vyberte v části **role** možnost *čtecí modul cloudových dat Azure ve jaře* .
5. Přiřaďte přístup k `User, group, or service principal` nebo `User assigned managed identity` podle typu uživatele. Vyhledejte a vyberte možnost uživatel.  
6. Klikněte na `Save`.

   ![přiřazení – role](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-data-plane"></a>Přístup k rovině dat

Jakmile se uživateli AAD přiřadí role *čtečky dat cloudu Azure jaře* , můžou se zákazníci přihlásit do Azure CLI pomocí uživatele, instančního objektu nebo spravované identity.  Přístup k přístupovému tokenu získáte v tématu [ověření Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) .  Pak použijte následující příkazy.

```azurecli
az login
az account get-access-token
```

V současné době rozhraní příkazového řádku podporuje výchozí koncové body konfiguračního serveru a registru služby. Další informace najdete v tématu [koncové body připravené pro produkční](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)prostředí. 

Zákazníci mohou také získat úplný seznam podporovaných koncových bodů konfiguračního serveru a registru služby přístupem k koncovým bodům:
* *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/*
* *https://SERVICE_NAME.Root_Endpoint/config/actuator/* 

Přístupový token v hlavičce poskytuje autorizaci. Podporovaná je jenom metoda GET.

Můžete například získat přístup ke koncovému bodu, jako *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/health* je třeba zobrazení stavu Eureka.

V závislosti na různých typech cloudu se zobrazují různé kořenové koncové body.

| Cloud          | Kořenový koncový bod              |
| -------------- | -------------------------- |
| Veřejná         | svc.azuremicroservices.io  |
| Mooncake/Čína | svc.microservices.azure.cn |

Pokud je odpověď *401 neoprávněná*, zkontrolujte, jestli je role úspěšně přiřazená.  Bude to trvat několik minut, než se role projeví, nebo ověřit, že platnost přístupového tokenu nevypršela.

## <a name="next-steps"></a>Další kroky
* [Ověření Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [Koncové body připravené pro produkci](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>Viz také
* [Vytváření rolí a oprávnění](spring-cloud-howto-permissions.md)