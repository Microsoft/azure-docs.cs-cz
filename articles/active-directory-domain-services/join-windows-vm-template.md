---
title: Připojte virtuální počítač s Windows serverem k Azure Active Directory Domain Services | Microsoft Docs
description: Připojte virtuální počítač s Windows serverem ke spravované doméně pomocí šablon Azure Resource Manager.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 599d474b7c45274c87878c622149a86bc93af318
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612272"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Připojení virtuálního počítače s Windows serverem ke spravované doméně pomocí šablony Správce prostředků
V tomto článku se dozvíte, jak připojit virtuální počítač s Windows serverem k Azure AD Domain Services spravované doméně pomocí šablon Správce prostředků.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Před zahájením
K provedení úkolů uvedených v tomto článku budete potřebovat:
1. Platné **předplatné Azure**.
2. **Adresář služby Azure AD** – buď synchronizovaný s místním adresářem, nebo s adresářem jenom pro Cloud.
3. **Azure AD Domain Services** musí být povolený pro adresář služby Azure AD. Pokud jste to ještě neudělali, postupujte podle všech úkolů popsaných v [průvodci Začínáme](tutorial-create-instance.md).
4. Ujistěte se, že jste nakonfigurovali IP adresy spravované domény jako servery DNS pro virtuální síť. Další informace najdete v tématu [Postup aktualizace nastavení DNS pro virtuální síť Azure](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network) .
5. Dokončete kroky potřebné k [synchronizaci hesel s Azure AD Domain Services spravované doméně](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="install-and-configure-required-tools"></a>Instalace a konfigurace požadovaných nástrojů
K provedení kroků popsaných v tomto dokumentu můžete použít kteroukoli z následujících možností:
* **Azure PowerShell**: [Instalace a konfigurace](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Azure CLI**: [Instalace a konfigurace](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>Možnost 1: Zřídit nový virtuální počítač s Windows serverem a připojit ho ke spravované doméně
**název šablony pro rychlý Start**: [201 – virtuální počítač – připojení k doméně](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Pokud chcete nasadit virtuální počítač s Windows serverem a připojit ho ke spravované doméně, proveďte následující kroky:
1. Přejděte do [šablony pro rychlé](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)zprovoznění.
2. Klikněte na **Deploy to Azure** (Nasadit do Azure).
3. Na stránce **vlastní nasazení** zadejte požadované informace pro zřízení virtuálního počítače.
4. Vyberte **předplatné Azure** , ve kterém chcete virtuální počítač zřídit. Vyberte stejné předplatné Azure, ve kterém jste povolili Azure AD Domain Services.
5. Vyberte existující **skupinu prostředků** nebo vytvořte novou.
6. Vyberte **umístění** , do kterého chcete nasadit nový virtuální počítač.
7. V části **stávající název virtuální**sítě zadejte virtuální síť, ve které jste nasadili Azure AD Domain Services spravovanou doménu.
8. V části **název existující podsítě**zadejte podsíť ve virtuální síti, kam chcete nasadit tento virtuální počítač. Nevybírejte podsíť brány ve virtuální síti. Nevybírejte také vyhrazenou podsíť, ve které je nasazená vaše spravovaná doména.
9. Do pole **předpona popisku DNS**zadejte název hostitele virtuálního počítače, který se zřizuje. Například "contoso-Win".
10. Vyberte odpovídající **Velikost virtuálního počítače** pro virtuální počítač.
11. V poli **doména pro připojení**zadejte název domény DNS vaší spravované domény.
12. V části **uživatelské jméno domény**zadejte název uživatelského účtu ve spravované doméně, který se má použít k připojení virtuálního počítače ke spravované doméně.
13. Do pole **heslo domény**zadejte heslo účtu uživatele domény, na který odkazuje parametr domainUsername.
14. Volitelné: Můžete zadat **cestu organizační jednotky** k vlastní organizační jednotce, do které se má virtuální počítač přidat. Pokud nezadáte hodnotu pro tento parametr, virtuální počítač se přidá do výchozí organizační jednotky **počítače AAD DC** ve spravované doméně.
15. Do pole **uživatelské jméno správce virtuálního počítače** zadejte název účtu místního správce pro virtuální počítač.
16. V poli **heslo správce virtuálního počítače** zadejte heslo místního správce pro virtuální počítač. Zadejte silné heslo místního správce virtuálního počítače, které chcete chránit před útoky hrubou silou hesla.
17. Klikněte na **Souhlasím s výše uvedenými podmínkami a ujednáními**.
18. Kliknutím na **koupit** zřídíte virtuální počítač.

> [!WARNING]
> **Pořídí hesla s opatrností.**
> Soubor parametrů šablony obsahuje hesla pro doménové účty a hesla místních správců pro virtuální počítač. Ujistěte se, že tento soubor nezůstane ve sdílených složkách nebo na jiných sdílených umístěních. Až dokončíte nasazení virtuálních počítačů, doporučujeme tento soubor vyřadit.
>

Po úspěšném dokončení nasazení se nově zřízený virtuální počítač s Windows připojí ke spravované doméně.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>Možnost 2: Připojit stávající virtuální počítač s Windows serverem ke spravované doméně
**Šablona pro rychlý Start**: [201 – virtuální počítač – připojení k doméně – existující](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

Pokud se chcete připojit k existujícímu virtuálnímu počítači s Windows serverem ke spravované doméně, proveďte následující kroky:
1. Přejděte do [šablony pro rychlé](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)zprovoznění.
2. Klikněte na **Deploy to Azure** (Nasadit do Azure).
3. Na stránce **vlastní nasazení** zadejte požadované informace pro zřízení virtuálního počítače.
4. Vyberte **předplatné Azure** , ve kterém chcete virtuální počítač zřídit. Vyberte stejné předplatné Azure, ve kterém jste povolili Azure AD Domain Services.
5. Vyberte existující **skupinu prostředků** nebo vytvořte novou.
6. Vyberte **umístění** , do kterého chcete nasadit nový virtuální počítač.
7. V poli **Seznam virtuálních počítačů** zadejte názvy existujících virtuálních počítačů, které se mají připojit ke spravované doméně. Jednotlivé názvy virtuálních počítačů můžete oddělit čárkami. Například **Contoso-web, contoso-API**.
8. Do pole **uživatelské jméno pro připojení k doméně**zadejte název uživatelského účtu ve spravované doméně, který se má použít k připojení virtuálního počítače ke spravované doméně.
9. Do pole **heslo uživatele pro připojení k doméně**zadejte heslo účtu uživatele domény, na který odkazuje parametr domainUsername.
10. Do pole **plně kvalifikovaný název domény**zadejte název domény DNS vaší spravované domény.
11. Volitelné: Můžete zadat **cestu organizační jednotky** k vlastní organizační jednotce, do které se má virtuální počítač přidat. Pokud nezadáte hodnotu pro tento parametr, virtuální počítač se přidá do výchozí organizační jednotky **počítače AAD DC** ve spravované doméně.
12. Klikněte na **Souhlasím s výše uvedenými podmínkami a ujednáními**.
13. Kliknutím na **koupit** zřídíte virtuální počítač.

> [!WARNING]
> **Pořídí hesla s opatrností.**
> Soubor parametrů šablony obsahuje hesla pro doménové účty a hesla místních správců pro virtuální počítač. Ujistěte se, že tento soubor nezůstane ve sdílených složkách nebo na jiných sdílených umístěních. Až dokončíte nasazení virtuálních počítačů, doporučujeme tento soubor vyřadit.
>

Po úspěšném dokončení nasazení jsou zadané virtuální počítače s Windows připojené ke spravované doméně.


## <a name="related-content"></a>Související obsah
* [Přehled Azure PowerShellu](/powershell/azure/overview)
* [Šablona rychlý Start pro Azure – připojení domény k novému virtuálnímu počítači](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Šablona Azure pro rychlý Start – připojení ke stávajícím virtuálním počítačům v doméně](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../azure-resource-manager/resource-group-template-deploy.md)
