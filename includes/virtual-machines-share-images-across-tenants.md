---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 499aeccdf00980eeb66ac6ee06e45267fd515143
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546751"
---
Sdílené Galerie obrázků vám umožní sdílení obrázků s využitím RBAC. RBAC můžete použít ke sdílení imagí v rámci vašeho tenanta a dokonce i jednotlivci mimo vašeho tenanta. Ale pokud ho chcete sdílet imagí mimo vašeho tenanta Azure ve velkém měřítku, měli byste vytvořit registrace aplikace k usnadnění sdílení.  Registrace aplikace s využitím můžete povolit složitější scénáře sdílení, jako jsou: 

* Správa sdílených bitových kopií, když jedna společnost získá jinou a infrastrukturu Azure se pak rozdělí mezi samostatné tenanty. 
* Partneři Azure ke správě infrastruktury Azure zastoupení svých zákazníků. Přizpůsobení imagí se provádí v rámci tenanta partnery, ale nasazení infrastruktury se stane v tenantovi zákazníka. 


## <a name="create-the-app-registration"></a>Vytvoření registrace aplikace

Vytvoření registrace aplikace, který se použije ke sdílení prostředků Galerie obrázků obě klienty.
1. Otevřít [registrace aplikací (preview) na webu Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Vyberte **registrace nové** v nabídce v horní části stránky.
1. V **název**, typ *myGalleryApp*.
1. V **podporovaných typů účtu**vyberte **účty v jakékoli organizaci adresáři a osobní účty Microsoft**.
1. V **identifikátor URI pro přesměrování**, typ *https://www.microsoft.com* a pak vyberte **zaregistrovat**. Po registraci aplikace se vytvořila, otevře se stránka s přehledem.
1. Na stránce s přehledem, zkopírujte **ID aplikace (klient)** a uložit pro použití později.   
1. Vyberte **certifikáty a tajné kódy**a pak vyberte **nový tajný kód klienta**.
1. V **popis**, typ *sdílený tajný klíč aplikace mezi tenanty Galerie obrázků*.
1. V **Expires**, ponechte výchozí hodnotu **v 1 rok** a pak vyberte **přidat**.
1. Hodnota tajného kódu zkopírujte a uložte ho na bezpečném místě. Nejde ho načíst opustíte na stránce.


Udělit oprávnění registrace aplikací k použití Galerie sdílené bitové kopie.
1. Na webu Azure Portal vyberte galerii sdílené bitové kopie, kterou chcete sdílet s jiným tenantem.
1. Vyberte **vyberte řízení přístupu (IAM)** a v části **přidat přiřazení role** vyberte *přidat*. 
1. V části **Role**vyberte **čtečky**.
1. V části **přiřadit přístup:**, nechte pole jako **uživatele, skupinu nebo instanční objekt služby Azure AD**.
1. V části **vyberte**, typ *myGalleryApp* a vyberte ji, když se zobrazí v seznamu. Jakmile budete hotovi, vyberte **Uložit**.


## <a name="give-tenant-2-access"></a>Poskytnout přístup Tenanta 2

Poskytnout 2 Tenanta přístup k aplikaci pomocí žádosti přihlášení v prohlížeči. Nahraďte *<Tenant2 ID>* s ID tenanta pro tenanta, který chcete sdílet vaše Galerie imagí s. Nahraďte *< ID aplikace (klient) >* s ID aplikace pro registraci aplikace, které jste vytvořili. Až budete hotovi, provedení nahrazení, vložte adresu URL do prohlížeče a postupujte podle pokynů přihlášení pro přihlášení do Tenanta 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

V [webu Azure portal](https://portal.azure.com) přihlaste jako Tenant 2 a umožněte přístup registraci aplikace do skupiny prostředků, ve kterém chcete vytvořit virtuální počítač.

1. Vyberte skupinu prostředků a potom vyberte **řízení přístupu (IAM)**. V části **přidat přiřazení role** vyberte **přidat**. 
1. V části **Role**, typ **Přispěvatel**.
1. V části **přiřadit přístup:**, nechte pole jako **uživatele, skupinu nebo instanční objekt služby Azure AD**.
1. V části **vyberte** typ *myGalleryApp* poté jej vyberte, když se zobrazí v seznamu. Jakmile budete hotovi, vyberte **Uložit**.

> [!NOTE]
> Budete muset počkat na verzi image, aby zcela dokončit právě vytvořené a replikované před stejné spravované image můžete vytvořit jinou verzi image.

