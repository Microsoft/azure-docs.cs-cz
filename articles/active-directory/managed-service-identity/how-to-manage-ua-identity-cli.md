---
title: Správa uživatel přiřazené Identity spravované služby (MSI) pomocí rozhraní příkazového řádku Azure
description: Podrobné pokyny o tom, jak vytvořit, vypsat a odstranit uživatel přiřazený spravovaná služba identit pomocí Azure CLI.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 23567c985f4f9df46ee7d80051c15dc5910a1ea8
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37904058"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-cli"></a>Vytvoření seznamu nebo odstranění uživatele přiřazeny identit pomocí Azure CLI

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identita spravované služby poskytuje služby Azure s využitím spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření služby, které podporují ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak vytvářet, seznamu a odstraňovat identity přiřazené uživateli, pomocí Azure CLI.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi přiřazenou systémem a identity přiřazené uživateli](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) než budete pokračovat.

- Spuštění ukázkové skripty rozhraní příkazového řádku, máte tři možnosti:

    - Použití [Azure Cloud Shell](../../cloud-shell/overview.md) z portálu Azure portal (viz další část).
    - Použijte vložené Azure Cloud Shell pomocí "Vyzkoušet" tlačítka, nachází v pravém horním rohu každý blok kódu.
    - [Nainstalujte nejnovější verzi 2.0 rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 nebo novější) Pokud byste radši chtěli použít místní konzoly příkazového řádku. Přihlaste se k Azure s využitím `az login`, pomocí účtu, který je přidružený k předplatnému Azure, ve které chcete nasadit identity přiřazené uživateli.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživateli 

K vytvoření identity přiřazené uživateli, použijte [vytvoření az identity](/cli/azure/identity#az-identity-create) příkazu. `-g` Parametr určuje skupinu prostředků, kde k vytvoření identity přiřazené uživateli a `-n` parametr určuje její název. Nahradit `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` parametr hodnoty vlastními hodnotami:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Uživatel seznamu identit přiřazených

Seznam uživatelsky přiřazených identit, použijte [seznam identit az](/cli/azure/identity#az-identity-list) příkazu.  `-g` Parametr určuje skupinu prostředků, ve kterém byla vytvořena identity přiřazené uživateli.  Nahraďte `<RESOURCE GROUP>` s vlastní hodnotou:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
V odpovědi json identit uživatelů mají `"Microsoft.ManagedIdentity/userAssignedIdentities"` hodnotu pro klíč, `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-identity"></a>Odstranit identity přiřazené uživateli

Chcete-li odstranit identity přiřazené uživateli, použijte [az identity odstranit](/cli/azure/identity#az-identity-delete) příkazu.  Parametr - n Určuje její název a parametr -g Určuje skupinu prostředků, ve kterém byla vytvořena identity přiřazené uživateli.  Nahradit `<USER ASSIGNED IDENTITY NAME>` a `<RESOURCE GROUP>` parametry hodnoty vlastními hodnotami:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Odstraňování identity přiřazené uživateli nedojde k odebrání odkazu, ze všech prostředků, který byl přiřazen. Odeberte prosím z virtuálního počítače/škálovací sady pomocí `az vm/vmss identity remove` příkaz

## <a name="related-content"></a>Související obsah

Úplný seznam příkazů rozhraní příkazového řádku Azure identity najdete v tématu [az identity](/cli/azure/identity).

Informace o tom, jak identity přiřazené uživateli přiřadit virtuálnímu počítači Azure najdete v tématu [konfigurace Identity spravované služby (MSI) pomocí rozhraní příkazového řádku Azure](qs-configure-cli-windows-vm.md#user-assigned-identity)


 
