---
title: Vytvoření aplikace spravované identity Azure Active Directory z Azure CLI
titleSuffix: An Azure Communication Services quickstart
description: Spravované identity umožňují autorizovat přístup ke komunikačním službám Azure z aplikací běžících na virtuálních počítačích Azure, aplikacích Function App a dalších prostředcích. Tento rychlý Start se zaměřuje na správu identity pomocí Azure CLI.
services: azure-communication-services
author: jbeauregardb
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: jbeauregardb
ms.reviewer: mikben
ms.openlocfilehash: 2ef5a3b162d62fa79ed01a156345070ee12b4862
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110674"
---
# <a name="authorize-access-with-managed-identity-to-your-communication-resource-in-your-development-environment"></a>Autorizaci přístupu ke spravované identitě ke zdroji komunikace ve vývojovém prostředí

Sada Azure identity SDK poskytuje podporu ověřování tokenů služby Azure Active Directory (Azure AD) pro sadu Azure SDK. Nejnovější verze sad Azure Communication Services SDK pro .NET, Java, Python a JavaScript se integrují s knihovnou identit Azure, aby poskytovaly jednoduché a zabezpečené prostředky pro získání tokenu OAuth 2,0 pro autorizaci žádostí o službu Azure Communication Services.

Výhodou sady Azure identity SDK je, že umožňuje používat stejný kód k ověřování napříč více službami, ať už vaše aplikace běží ve vývojovém prostředí nebo v Azure. Sada Azure identity SDK ověřuje objekt zabezpečení. Když váš kód běží v Azure, je objekt zabezpečení spravovaná identita pro prostředky Azure. Ve vývojovém prostředí neexistuje spravovaná identita, takže sada SDK ověřuje buď uživatele, nebo registrovanou aplikaci pro účely testování.

## <a name="prerequisites"></a>Požadavky

 - Azure CLI [Průvodce instalací](/cli/azure/install-azure-cli)
 - Účet Azure s aktivním předplatným. [Vytvořit účet zdarma](https://azure.microsoft.com/free)

## <a name="setting-up"></a>Nastavení

U prostředků Azure, které autorizujete, by měly být povolené spravované identity. Informace o tom, jak povolit spravované identity pro prostředky Azure, najdete v jednom z těchto článků:

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Šablona Azure Resource Manageru](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Sady Azure Resource Manager SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [App Services](../../app-service/overview-managed-identity.md)

## <a name="authenticate-a-registered-application-in-the-development-environment"></a>Ověřování registrované aplikace ve vývojovém prostředí

Pokud vaše vývojové prostředí nepodporuje jednotné přihlašování nebo přihlašování přes webový prohlížeč, můžete použít registrovanou aplikaci k ověření z vývojového prostředí.

### <a name="creating-an-azure-active-directory-registered-application"></a>Vytvoření registrované aplikace Azure Active Directory

Pokud chcete vytvořit registrovanou aplikaci z rozhraní příkazového řádku Azure, musíte být přihlášeni k účtu Azure, na kterém chcete provádět operace. K tomu můžete použít `az login` příkaz a zadat přihlašovací údaje v prohlížeči. Po přihlášení k účtu Azure z CLI můžeme zavolat `az ad sp create-for-rbac` příkaz k vytvoření registrované aplikace.

Následující příklady používají rozhraní příkazového řádku Azure CLI k vytvoření nové registrované aplikace.

```azurecli
az ad sp create-for-rbac --name <application-name> 
```

`az ad sp create-for-rbac`Příkaz vrátí seznam vlastností instančního objektu ve formátu JSON. Zkopírujte tyto hodnoty, abyste je mohli použít k vytvoření potřebných proměnných prostředí v dalším kroku.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```
> [!IMPORTANT]
> Rozšiřování přiřazení rolí Azure může trvat několik minut.

#### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Sada Azure identity SDK načítá hodnoty ze tří proměnných prostředí za běhu za účelem ověření aplikace. Následující tabulka popisuje hodnotu, která se má nastavit pro každou proměnnou prostředí.

|Proměnná prostředí|Hodnota
|-|-
|`AZURE_CLIENT_ID`|`appId` hodnota z generovaného JSON 
|`AZURE_TENANT_ID`|`tenant` hodnota z generovaného JSON
|`AZURE_CLIENT_SECRET`|`password` hodnota z generovaného JSON

> [!IMPORTANT]
> Po nastavení proměnných prostředí zavřete a znovu otevřete okno konzoly. Pokud používáte aplikaci Visual Studio nebo jiné vývojové prostředí, může být nutné ji restartovat, aby mohla zaregistrovat nové proměnné prostředí.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o ověřování](../concepts/authentication.md)

Můžete také chtít:

- [Další informace o službě Azure identity Library](/dotnet/api/overview/azure/identity-readme)