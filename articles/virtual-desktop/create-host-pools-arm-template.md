---
title: Fond hostitelů Virtuální plochy Windows Azure – Azure
description: Jak vytvořit fond hostitelů ve Virtuální desktopu Windows pomocí šablony Azure Resource Manager.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9ce6440989cbf962c474de2a6c90db4c485bf4a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292329"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Vytvoření fondu hostitelů pomocí šablony Azure Resource Manageru

Fondy hostitelů jsou kolekce jednoho nebo více identických virtuálních počítačů v prostředích klienta Windows Virtual Desktop. Každý fond hostitelů může obsahovat skupinu aplikací, se kterou mohou uživatelé pracovat stejně jako na fyzické ploše.

Podle pokynů v této části vytvořte fond hostitelů pro klienta Virtuální plochy Windows se šablonou Azure Resource Manager, kterou poskytuje Microsoft. V tomto článku se vám bude říkat, jak vytvořit fond hostitelů ve Windows Virtual Desktop, vytvořit skupinu prostředků s virtuálními počítači v předplatném Azure, připojit tyto virtuální počítače k doméně Služby AD a zaregistrovat virtuální počítače s Windows Virtual Desktop.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Co potřebujete ke spuštění šablony Azure Resource Manager

Před spuštěním šablony Azure Resource Manager uděláte následující:

- Kde je zdroj obrázku, který chcete použít. Je to z Galerie Azure nebo je to vlastní?
- Přihlašovací údaje pro připojení k doméně.
- Pověření pro virtuální plochu systému Windows.

Když vytvoříte fond hostitelů Virtuální plochy Windows pomocí šablony Azure Resource Manager, můžete vytvořit virtuální počítač z galerie Azure, spravované image nebo nespravované image. Další informace o tom, jak vytvářet image virtuálních počítačů, najdete [v tématu Příprava virtuálního pevného disku nebo virtuálního počítače s Windows k nahrání do Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) a vytvoření spravované [bitové kopie zobecněného virtuálního počítače v Azure](../virtual-machines/windows/capture-image-resource.md).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Spuštění šablony Azure Resource Manager pro zřizování nového fondu hostitelů

Chcete-li začít, přejděte na [tuto adresu URL GitHubu](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Nasazení šablony do Azure

Pokud nasazujete v předplatném Enterprise, posuňte se dolů a vyberte **Nasazení do Azure**a pak přeskočte dopředu vyplňte parametry na základě zdroje bitové kopie.

Pokud nasazujete v předplatném poskytovatele cloudových řešení, nasaďte se do Azure takto:

1. Posuňte se dolů a klikněte pravým tlačítkem myši **na Nasazení do Azure**a vyberte kopírovat umístění **odkazu**.
2. Otevřete textový editor, jako je Poznámkový blok, a vložte odkaz tam.
3. Hned zahttps://portal.azure.com/" " a před hashtag (#) zadejte na znaménko (@) následovaný název domény klienta. Zde je příklad formátu, který byste `https://portal.azure.com/@Contoso.onmicrosoft.com#create/`měli použít: .
4. Přihlaste se k portálu Azure jako uživatel s oprávněními správce/přispěvatele k předplatnému poskytovatele cloudových řešení.
5. Vložte odkaz, který jste zkopírovali do textového editoru, do adresního řádku.

Pokyny k tomu, které parametry byste měli zadat pro svůj scénář, naleznete v [souboru Readme](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)virtuální plochy systému Windows . Readme je vždy aktualizován s nejnovějšími změnami.

## <a name="assign-users-to-the-desktop-application-group"></a>Přiřazení uživatelů ke skupině aplikací klasické pracovní plochy

Po dokončení šablony GitHub Azure Resource Manager, přiřaďte přístup uživatele před zahájením testování celé relace plochy na virtuálních počítačích.

Nejprve [si stáhněte a importujte modul Windows Virtual Desktop PowerShell,](/powershell/windows-virtual-desktop/overview/) který se použije v relaci PowerShellu, pokud jste tak ještě neučinili.

Chcete-li přiřadit uživatele ke skupině aplikací klasické pracovní plochy, otevřete okno prostředí PowerShell a spusťte tuto rutinu pro přihlášení do prostředí Virtuální plochy systému Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Poté přidejte uživatele do skupiny aplikací pro stolní počítače pomocí této rutiny:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Hlavní název uživatele by měl odpovídat identitě uživatele ve službě user1@contoso.comAzure Active Directory (například). Pokud chcete přidat více uživatelů, musíte spustit tuto rutinu pro každého uživatele.

Po dokončení těchto kroků se uživatelé připřidáví do skupiny aplikací plochy mohou přihlásit k virtuální ploše systému Windows s podporovanými klienty vzdálené plochy a zobrazit prostředek pro plochu relace.

>[!IMPORTANT]
>Chcete-li zabezpečit prostředí Virtuální desktopwindows v Azure, doporučujeme neotevírat příchozí port 3389 na virtuálních počítačích. Virtuální plocha Windows nevyžaduje otevřený vstupní port 3389 pro přístup uživatelů k virtuálním počítačům hostitelského fondu. Pokud musíte otevřít port 3389 pro účely řešení potíží, doporučujeme použít [přístup k virtuálním ms just-in-time](../security-center/security-center-just-in-time.md).