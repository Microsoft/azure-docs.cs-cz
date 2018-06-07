---
title: Připojení k serveru systému Windows virtuálního počítače Azure Active Directory Domain Services | Microsoft Docs
description: Připojení virtuálního počítače s Windows serverem k spravované doméně pomocí šablony Azure Resource Manager.
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: b5d9e724fcbe3f872eee2f64bdec6894ef97e2e1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34587193"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Připojení virtuálního počítače Windows serveru ke spravované doméně pomocí šablony Resource Manageru
Tento článek ukazuje, jak připojit virtuální počítač Windows serveru k spravované doméně služby Azure AD Domain Services pomocí šablony Resource Manageru.

## <a name="before-you-begin"></a>Než začnete
Chcete-li provést úkoly vypsané v tomto článku, je třeba:
1. Platná **předplatné**.
2. **Adresář Azure AD** – buď synchronizovány s místní adresář nebo výhradně cloudový adresář.
3. **Azure AD Domain Services** musí být povolen pro adresář Azure AD. Pokud jste tak dosud neučinili, postupujte podle všechny úkoly popsané v [příručce Začínáme](active-directory-ds-getting-started.md).
4. Ujistěte se, že jste nakonfigurovali IP adresy spravované doméně jako servery DNS pro virtuální síť. Další informace najdete v tématu [postup aktualizace nastavení DNS pro virtuální síť Azure](active-directory-ds-getting-started-dns.md)
5. Dokončete kroky potřebné k [synchronizovat hesla k spravované doméně služby Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="install-and-configure-required-tools"></a>Nainstalujte a nakonfigurujte požadované nástroje
K provedení kroků uvedených v tomto dokumentu můžete použít některý z následujících možností:
* **Prostředí Azure PowerShell**: [instalace a konfigurace](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Rozhraní příkazového řádku Azure napříč platformami**: [instalace a konfigurace](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>Možnost 1: Zřizovat nové virtuální počítače Windows serveru a připojení ke spravované doméně
**Název šablony rychlý start**: [201-vm-připojení k doméně](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Nasazení virtuálního počítače s Windows serverem a připojení ke spravované doméně, proveďte následující kroky:
1. Přejděte na [šablony rychlý start](https://azure.microsoft.com/resources/templates/201-vm-domain-join/).
2. Klikněte na **Deploy to Azure** (Nasadit do Azure).
3. V **vlastní nasazení** zadejte požadované informace pro zřízení virtuálního počítače.
4. Vyberte **předplatného Azure** v pro zřízení virtuálního počítače. Vyberte stejného předplatného Azure, ve kterém jste povolili službu Azure AD Domain Services.
5. Vybrat existující **skupiny prostředků** nebo vytvořte novou.
6. Vyberte **umístění** ve které chcete nasadit nový virtuální počítač.
7. V **existující název virtuální sítě**, zadejte virtuální síť, ve které jste nasadili vaší spravované domény služby Azure AD Domain Services.
8. V **existující název podsítě**, zadejte podsíť v rámci virtuální sítě, kde chcete nasadit tento virtuální počítač. Nevybírejte podsíť brány ve virtuální síti. Navíc nevybírejte vyhrazené podsíť, ve kterém je nasazený vaší spravované domény.
9. V **předpona DNS popisek**, zadejte název hostitele pro virtuální počítač, se zřídí. Například 'contoso-win'.
10. Vyberte odpovídající **velikost virtuálního počítače** pro virtuální počítač.
11. V **k připojení k doméně**, zadejte název domény DNS vaší spravované domény.
12. V **uživatelské jméno domény**, zadejte název uživatelského účtu na vaší spravované domény, který se má použít pro připojení virtuálních počítačů k spravované doméně.
13. V **heslo domény**, zadejte heslo uživatelského účtu domény odkazuje parametr 'domainUsername'.
14. Volitelné: Můžete zadat **organizační jednotky cesta** do vlastní organizační jednotky, ke které chcete přidat virtuální počítač. Pokud nezadáte hodnotu tohoto parametru, je virtuální počítač přidat do výchozí **počítače řadiče domény AAD** organizační jednotku na spravované domény.
15. V **uživatelské jméno správce virtuálních počítačů** pole, zadejte název účtu místního správce pro virtuální počítač.
16. V **heslo správce virtuálních počítačů** pole, zadejte heslo místního správce pro virtuální počítač. Zadejte heslo pro virtuální počítač chránit před útoky hrubou silou heslo silné místního správce.
17. Klikněte na tlačítko **souhlasím s podmínkami a ujednáními výše uvedených**.
18. Klikněte na tlačítko **nákupu** ke zřízení virtuálního počítače.

> [!WARNING]
> **Zpracování hesel opatrně.**
> Parametr soubor šablony obsahuje hesla pro doménové účty a hesla místního správce pro virtuální počítač. Zkontrolujte, zda že jste nenechávejte tento soubor ležící kolem ve sdílených složkách nebo jiné sdílené umístění. Doporučujeme, abyste vyřazení tento soubor po dokončení nasazení virtuálních počítačů.
>

Po úspěšném dokončení nasazení nově zřízeného virtuálního počítače Windows je připojený k spravované doméně.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>Možnost 2: Připojení k existující virtuální počítač Windows serveru ke spravované doméně
**Šablony rychlý start**: [201-virtuálních počítačů domain-připojení k existující](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

K připojení k existující virtuální počítač Windows serveru ke spravované doméně, proveďte následující kroky:
1. Přejděte na [šablony rychlý start](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
2. Klikněte na **Deploy to Azure** (Nasadit do Azure).
3. V **vlastní nasazení** zadejte požadované informace pro zřízení virtuálního počítače.
4. Vyberte **předplatného Azure** v pro zřízení virtuálního počítače. Vyberte stejného předplatného Azure, ve kterém jste povolili službu Azure AD Domain Services.
5. Vybrat existující **skupiny prostředků** nebo vytvořte novou.
6. Vyberte **umístění** ve které chcete nasadit nový virtuální počítač.
7. V **seznamu virtuálních počítačů** pole, zadejte názvy existujících virtuálních počítačů k spravované doméně. Oddělte jednotlivé názvy virtuálních počítačů pomocí čárkou. Například **web společnosti contoso, contoso-api**.
8. V **uživatelské jméno domény spojení**, zadejte název uživatelského účtu na vaší spravované domény, který se má použít pro připojení virtuálních počítačů k spravované doméně.
9. V **heslo uživatele domény připojit**, zadejte heslo uživatelského účtu domény odkazuje parametr 'domainUsername'.
10. V **domény plně kvalifikovaný název domény**, zadejte název domény DNS vaší spravované domény.
11. Volitelné: Můžete zadat **organizační jednotky cesta** do vlastní organizační jednotky, ke které chcete přidat virtuální počítač. Pokud nezadáte hodnotu tohoto parametru, je virtuální počítač přidat do výchozí **počítače řadiče domény AAD** organizační jednotku na spravované domény.
12. Klikněte na tlačítko **souhlasím s podmínkami a ujednáními výše uvedených**.
13. Klikněte na tlačítko **nákupu** ke zřízení virtuálního počítače.

> [!WARNING]
> **Zpracování hesel opatrně.**
> Parametr soubor šablony obsahuje hesla pro doménové účty a hesla místního správce pro virtuální počítač. Zkontrolujte, zda že jste nenechávejte tento soubor ležící kolem ve sdílených složkách nebo jiné sdílené umístění. Doporučujeme, abyste vyřazení tento soubor po dokončení nasazení virtuálních počítačů.
>

Po úspěšném dokončení nasazení zadaný virtuální počítače Windows jsou připojeny k spravované doméně.


## <a name="related-content"></a>Související obsah
* [Přehled prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.0)
* [Šablony Azure rychlý start - nový virtuální počítač připojit k doméně](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Šablony Azure rychlý start - stávající virtuální počítače připojit k doméně](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../azure-resource-manager/resource-group-template-deploy.md)
