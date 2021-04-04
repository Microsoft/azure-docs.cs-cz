---
title: Azure Resource Manager fondu hostitelů Windows Virtual Desktop (Classic) – Azure
description: Postup vytvoření fondu hostitelů ve virtuální ploše Windows (Classic) pomocí šablony Azure Resource Manager.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b9999ae92840d79bb19464216c0f28504011b3f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008317"
---
# <a name="create-a-host-pool-in-windows-virtual-desktop-classic-with-an-azure-resource-manager-template"></a>Vytvoření fondu hostitelů ve virtuální ploše Windows (Classic) pomocí šablony Azure Resource Manager

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows.

Fondy hostitelů jsou kolekce jednoho nebo více identických virtuálních počítačů v prostředích klienta virtuálních počítačů s Windows. Každý fond hostitelů může obsahovat skupinu aplikací, se kterou můžou uživatelé interaktivně pracovat, jako by na fyzickém počítači.

Postupujte podle pokynů v této části a vytvořte fond hostitelů pro tenanta virtuálních klientů Windows se šablonou Azure Resource Manager poskytnutou Microsoftem. V tomto článku se dozvíte, jak vytvořit fond hostitelů na virtuálním počítači s Windows, vytvořit skupinu prostředků s virtuálními počítači v předplatném Azure, připojit tyto virtuální počítače k doméně AD a zaregistrovat virtuální počítače s využitím virtuálního klienta Windows.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Co potřebujete ke spuštění šablony Azure Resource Manager

Před spuštěním šablony Azure Resource Manager se ujistěte, že znáte následující věci:

- Kde je zdroj obrázku, který chcete použít. Je to z Galerie Azure nebo je vlastní?
- Vaše přihlašovací údaje k doméně
- Přihlašovací údaje k virtuálnímu počítači s Windows

Když vytvoříte fond hostitelů virtuálních počítačů s Windows pomocí šablony Azure Resource Manager, můžete vytvořit virtuální počítač z Galerie Azure, spravované bitové kopie nebo nespravované image. Další informace o tom, jak vytvořit image virtuálních počítačů, najdete v tématu [Příprava virtuálního pevného disku (VHD) Windows nebo VHDX pro nahrání do Azure](../../virtual-machines/windows/prepare-for-upload-vhd-image.md) a [Vytvoření spravované image zobecněného virtuálního počítače v Azure](../../virtual-machines/windows/capture-image-resource.md).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Spusťte šablonu Azure Resource Manager pro zřízení nového fondu hostitelů.

Začněte tím, že přejdete na [tuto adresu URL GitHubu](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Nasazení šablony do Azure

Pokud nasazujete v rámci podnikového předplatného, posuňte se dolů a vyberte **nasadit do Azure** a pak přeskočte před vyplněním parametrů na základě vašeho zdroje imagí.

Pokud nasazujete v rámci předplatného poskytovatele Cloud Solution Provider, proveďte následující postup nasazení do Azure:

1. Posuňte se dolů a klikněte pravým tlačítkem myši na **nasadit do Azure** a pak vyberte **Kopírovat umístění odkazu**.
2. Otevřete textový editor, jako je Poznámkový blok, a vložte odkaz sem.
3. Hned za znakem " https://portal.azure.com/ " a před hashtagem (#) zadejte znak po znaku (@) následovaný názvem domény klienta. Tady je příklad formátu, který byste měli použít: `https://portal.azure.com/@Contoso.onmicrosoft.com#create/` .
4. Přihlaste se k Azure Portal jako uživatel s oprávněním správce/Přispěvatel k předplatnému poskytovatele Cloud Solution Provider.
5. Vložte odkaz, který jste zkopírovali do textového editoru, do adresního řádku.

Pokyny týkající se parametrů, které byste měli zadat pro váš scénář, najdete v [souboru Readme](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)pro virtuální počítače s Windows. Soubor Readme se vždycky aktualizuje o nejnovější změny.

## <a name="assign-users-to-the-desktop-application-group"></a>Přiřazení uživatelů ke skupině desktopových aplikací

Po dokončení šablony Azure Resource Manager GitHubu přiřaďte uživatelský přístup před zahájením testování klientů s úplnými relacemi na virtuálních počítačích.

Nejdřív [Stáhněte a importujte modul PowerShellu virtuálního počítače s Windows](/powershell/windows-virtual-desktop/overview/) , který chcete použít v relaci PowerShellu, pokud jste to ještě neudělali.

Pokud chcete přiřadit uživatele do skupiny desktopové aplikace, otevřete okno PowerShellu a spuštěním této rutiny se přihlaste k prostředí virtuálních počítačů s Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Pak přidejte uživatele do skupiny desktopových aplikací pomocí této rutiny:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Hlavní název uživatele (UPN) by měl odpovídat identitě uživatele v Azure Active Directory (například user1@contoso.com ). Pokud chcete přidat více uživatelů, musíte tuto rutinu spustit pro každého uživatele.

Po dokončení těchto kroků se uživatelé přidaní do skupiny desktopových aplikací můžou přihlásit k virtuální ploše Windows pomocí podporovaných klientů vzdálené plochy a zobrazit prostředek pro plochu relace.

>[!IMPORTANT]
>Pro lepší zabezpečení prostředí virtuálních počítačů s Windows v Azure doporučujeme na svých virtuálních počítačích neotevírat port 3389 pro příchozí spojení. Virtuální počítač s Windows nevyžaduje pro přístup k virtuálním počítačům fondu hostitelů otevřený příchozí port 3389. Pokud musíte pro účely řešení potíží otevřít port 3389, doporučujeme použít [přístup k virtuálnímu počítači za běhu](../../security-center/security-center-just-in-time.md).