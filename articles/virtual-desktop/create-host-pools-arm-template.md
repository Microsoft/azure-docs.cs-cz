---
title: Vytvoření fondu hostitele Preview virtuální plochy Windows pomocí šablony Azure Resource Manageru – Azure
description: Postup vytvoření fondu hostitele v náhledu virtuální plochy Windows pomocí šablony Azure Resource Manageru.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: ba98328002cafbcede855b1187881d39f1de8fc5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796422"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Vytvoření fondu hostitelů pomocí šablony Azure Resource Manageru

Hostitel fondy jsou kolekce jednoho nebo víc stejných virtuálních počítačů v prostředí klienta Windows virtuální plochy, ve verzi Preview. Každý hostitel fond může obsahovat skupinu aplikací, které mohou uživatelé komunikovat s stejně jako ve fyzických stolním počítači.

Postupujte podle pokynů v této části a vytvořit tak fond hostitele pro virtuální plochy Windows tenanta pomocí šablony Azure Resource Manageru poskytované společností Microsoft. V tomto článku zjistíte, jak vytvoření hostitele fondu ve virtuální plochy Windows, vytvořte skupinu prostředků s virtuálními počítači v rámci předplatného Azure, připojit tyto virtuální počítače k doméně AD a zaregistrovat virtuální počítače s Windows virtuálního klienta.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Co je potřeba spustit šablonu Azure Resource Manageru

Ujistěte se, že před spuštěním šablony Azure Resource Manageru znáte následující věci:

- Pokud je zdroj image, kterou chcete použít. Jde o aktivitu z Galerie Azure, nebo se jedná vlastní?
- Přihlašovací údaje pro připojení k vaší domény.
- Vaše přihlašovací údaje virtuálního klienta Windows.

Při vytváření fondu hostitele virtuální plochy Windows pomocí šablony Azure Resource Manageru můžete vytvořit virtuální počítač z Galerie Azure, bitové kopie spravované nebo nespravované image. Další informace o tom, jak vytvořit Image virtuálních počítačů najdete v tématu [Příprava Windows VHD nebo VHDX, který chcete nahrát do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) a [vytvoření spravované image zobecněného virtuálního počítače v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Spustit šablonu Azure Resource Manageru pro zřízení nového fondu hostitele

Pokud chcete začít, přejděte na [tuto adresu URL Githubu](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Nasazení šablony do Azure

Pokud nasazujete v rámci předplatného Enterprise, posuňte se dolů a vyberte **nasadit do Azure**, pak přeskočit napřed vyplnit parametry podle zdroje obrázku.

Pokud nasazení provádíte v rámci předplatného poskytovatele Cloud Solution Provider, postupujte podle těchto kroků nasadíte do Azure:

1. Posuňte se dolů a klikněte pravým tlačítkem na **nasadit do Azure**a pak vyberte **umístění propojení kopírování**.
2. Otevřete textový editor, například Poznámkový blok a vložte odkaz existuje.
3. Hned po "https://portal.azure.com/" a zadejte před hashtagem (#) zavináč (@) následovaný název domény tenantu. Tady je příklad formátu, abyste používali: https://portal.azure.com/@Contoso.onmicrosoft.com#create/.
4. Přihlaste se k webu Azure portal jako uživatel s oprávněními správce nebo přispěvatele pro předplatné poskytovatele Cloud Solution Provider.
5. Vložte odkaz, který jste zkopírovali do textového editoru, a do adresního řádku.

Informace o parametrech, které byste měli zadat pro váš scénář, naleznete v tématu virtuální plochy Windows [souboru Readme](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md). Soubor Readme se aktualizuje vždy s nejnovějšími změnami.

## <a name="assign-users-to-the-desktop-application-group"></a>Přiřazení uživatelů ke skupině pro aplikace klasické pracovní plochy

Po dokončení šablonu GitHub Azure Resource Manageru přiřadíte přístup uživatelům před začátkem testování celé relace plochy na virtuálních počítačích.

Nejprve je potřeba [stáhněte a naimportujte modul Powershellu virtuální plochy Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) použít v relaci Powershellu, pokud jste tak již neučinili.

Pokud chcete přiřadit ke skupině pro aplikace klasické pracovní plochy uživatele, otevřete okno Powershellu a spusťte tuto rutinu pro přihlášení k prostředí virtuálního klienta Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Dále nastavte kontext do tenanta skupiny zadané v šabloně Azure Resource Manageru pomocí této rutiny:

```powershell
Set-RdsContext -TenantGroupName <Tenant Group name>
```

Potom přidáte uživatele do skupiny aplikace klasické pracovní plochy s touto rutinou:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Hlavním názvem uživatele by měl odpovídat identitu uživatele ve službě Azure Active Directory (například user1@contoso.com). Pokud chcete přidat více uživatelů, musíte tuto rutinu spustíte pro každého uživatele.

Po dokončení těchto kroků můžete uživatelé přidaní do skupiny aplikací klasické pracovní plochy přihlášení k virtuálnímu klientovi Windows pomocí podporované klienty vzdálené plochy a zobrazit zdroj pro relaci plochy.

>[!IMPORTANT]
>Pomáhají zabezpečit vaše prostředí virtuálního klienta Windows v Azure, doporučujeme, abyste že na virtuálních počítačích není otevřít příchozí port 3389. Virtuální Desktop Windows nevyžaduje otevřít příchozí port 3389 pro uživatele pro přístup k fondu hostitele virtuálních počítačů. Pokud musíte otevřít port 3389 pro účely odstraňování potíží, doporučujeme vám použít [přístup k virtuálním počítačům just-in-time](https://docs.microsoft.com/en-us/azure/security-center/security-center-just-in-time).