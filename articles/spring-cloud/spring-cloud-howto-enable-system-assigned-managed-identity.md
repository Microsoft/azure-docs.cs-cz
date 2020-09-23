---
title: Povolení spravované identity přiřazené systémem pro aplikaci Azure Spring Cloud
description: Jak povolit spravovanou identitu přiřazenou systémem pro aplikaci.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/13/2020
ms.custom: devx-track-java
ms.openlocfilehash: bff98ea3470110bc29f75361fb3a2adc685e2602
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90888571"
---
# <a name="how-to-enable-system-assigned-managed-identity-for-azure-spring-cloud-application"></a>Postup povolení spravované identity přiřazené systémem pro aplikaci Azure jaře Cloud

**Tento článek se týká:** ✔️ Java ✔️ C #

Spravované identity pro prostředky Azure poskytují automaticky spravovanou identitu v Azure Active Directory k prostředkům Azure, jako je například vaše aplikace pro jarní Cloud v Azure. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu.

V tomto článku se dozvíte, jak povolit a zakázat spravované identity přiřazené systémem pro cloudovou aplikaci Azure pomocí Azure Portal a rozhraní příkazového řádku (dostupného z verze 0.2.4).

## <a name="prerequisites"></a>Požadavky
Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Budete potřebovat nasazenou instanci cloudu Azure pro jaře. Dokončete [nasazení pomocí Azure CLI pomocí rychlého](spring-cloud-quickstart.md)startu.

## <a name="add-a-system-assigned-identity"></a>Přidat identitu přiřazenou systémem
Vytvoření aplikace s identitou přiřazenou systémem vyžaduje nastavení další vlastnosti v aplikaci.

### <a name="using-azure-portal"></a>Pomocí webu Azure Portal
Pokud chcete nastavit spravovanou identitu v [Azure Portal](https://portal.azure.com/), nejdřív vytvořte aplikaci a pak tuto funkci povolte.

1. Vytvořte aplikaci na portálu obvyklým způsobem. Přejděte na portál na portálu.
2. Přejděte dolů do skupiny **Nastavení** v levém navigačním podokně.
3. Vyberte **Identita**.
4. V rámci karty **přiřazené systémem** přepněte **stav** na *zapnuto*. Klikněte na **Uložit**.

 ![Spravovaná identita na portálu](./media/spring-cloud-managed-identity/identity-1.png)

### <a name="using-azure-cli"></a>Použití Azure CLI
Spravovanou identitu přiřazenou systémem můžete povolit během vytváření aplikací nebo v existující aplikaci.

**Povolit spravovanou identitu přiřazenou systémem během vytváření aplikace**

Následující příklad vytvoří aplikaci s názvem *APP_NAME* se spravovanou identitou přiřazenou systémem, jak je požadováno `--assign-identity` parametrem.

```azurecli
az spring-cloud app create -n app_name -s service_name -g resource_group_name --assign-identity
```

**Povolit spravovanou identitu přiřazenou systémem v existující aplikaci** Pomocí `az spring-cloud app identity assign` příkazu můžete pro existující aplikaci povolit identitu přiřazenou systémem.

```azurecli
az spring-cloud app identity assign -n app_name -s service_name -g resource_group_name
```

## <a name="obtain-tokens-for-azure-resources"></a>Získání tokenů pro prostředky Azure
Aplikace může pomocí spravované identity získat tokeny pro přístup k jiným prostředkům chráněným Azure Active Directory, jako je například Azure Key Vault. Tyto tokeny reprezentují aplikaci, která přistupuje k prostředkům, ne každému konkrétnímu uživateli aplikace.

Možná budete muset [nakonfigurovat cílový prostředek, aby povoloval přístup z vaší aplikace](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/howto-assign-access-portal). Pokud například požadujete token pro přístup k Key Vault, ujistěte se, že jste přidali zásadu přístupu, která zahrnuje identitu vaší aplikace. V opačném případě budou volání Key Vault odmítnuta, a to i v případě, že obsahují token. Další informace o tom, které prostředky podporují Azure Active Directory tokeny, najdete v tématu [služby Azure, které podporují ověřování Azure AD](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication).

Azure jaře Cloud sdílí stejný koncový bod pro získání tokenu s virtuálním počítačem Azure. K získání tokenu doporučujeme použít sadu Java SDK nebo starty pružinového spouštění.  Podívejte [se, jak používat token virtuálních počítačů](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token) pro různé příklady kódu a skriptu a pokyny k důležitým tématům, jako je zpracování vypršení platnosti tokenu a chyby protokolu HTTP.

Doporučené: k získání tokenů použijte sadu Java SDK nebo starty pružinového spuštění.  Podívejte se na ukázky v [následujících krocích](#next-steps).

## <a name="disable-system-assigned-identity-from-an-app"></a>Zakázání identity přiřazené systémem z aplikace
Když odeberete identitu přiřazenou systémem, odstraní se taky z Azure AD. Při odstranění prostředku aplikace se automaticky odeberou identity přiřazené systémem ze služby Azure AD.

### <a name="using-azure-portal"></a>Pomocí webu Azure Portal
Pokud chcete odebrat spravovanou identitu přiřazenou systémem z aplikace, která ji už nepotřebuje:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu přidruženého k předplatnému Azure, které obsahuje instanci Azure jaře Cloud.
1. Přejděte k požadovanému virtuálnímu počítači a vyberte možnost **Identita**.
1. V části stav **přiřazení systému** / **Status**vyberte **vypnuto** a potom klikněte na **Uložit**:

 ![Spravovaná identita](./media/spring-cloud-managed-identity/remove-identity.png)

### <a name="using-azure-cli"></a>Použití Azure CLI
Pokud chcete odebrat spravovanou identitu přiřazenou systémem z aplikace, která ji už nepotřebuje, použijte následující příkaz:
```azurecli
az spring-cloud app identity remove -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Další kroky

* [Přístup k Azure Key Vault se spravovanými identitami ve jarním startu Starter](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets/README.md#use-msi--managed-identities)
* [Další informace o spravovaných identitách pro prostředky Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Použití spravovaných identit pomocí sady Java SDK](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)

