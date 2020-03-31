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
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73903708"
---
Ale pokud chcete sdílet image mimo vašeho tenanta Azure, ve velkém měřítku, měli byste vytvořit registraci aplikace pro usnadnění sdílení.  Použití registrace aplikace může povolit složitější scénáře sdílení, například: 

* Správa sdílených bitových kopií, když jedna společnost získá jinou a infrastruktura Azure se rozkládá mezi samostatné klienty. 
* Partneři Azure spravují infrastrukturu Azure jménem svých zákazníků. Přizpůsobení iniciálních bitových kopií se provádí v rámci klienta partnerů, ale nasazení infrastruktury se stane v tenantovi zákazníka. 


## <a name="create-the-app-registration"></a>Vytvoření registrace aplikace

Vytvořte registraci aplikace, kterou budou oba klienti používat ke sdílení prostředků galerie obrázků.
1. Otevřete [registrace aplikací (preview) na webu Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. V horní části stránky vyberte **Nová registrace.**
1. Do **pole Název**zadejte příkaz *myGalleryApp*.
1. V **části Podporované typy účtů**vyberte možnost Účty v **libovolném organizačním adresáři a osobních účtech Microsoft**.
1. Do pole Identifikátor *https://www.microsoft.com* URI **přesměrování**zadejte a vyberte příkaz **Registrovat**. Po vytvoření registrace aplikace se otevře stránka s přehledem.
1. Na stránce s přehledem zkopírujte **ID aplikace (klienta)** a uložte pro pozdější použití.   
1. Vyberte **certifikáty & tajných kódů**a potom vyberte **Nový tajný klíč klienta**.
1. V **poli Popis**zadejte tajný klíč aplikace mezi *klienty galerie sdílených obrázků*.
1. V **části Expires**ponechejte výchozí hodnotu **Za 1 rok** a pak vyberte **Přidat**.
1. Zkopírujte hodnotu tajného klíče a uložte jej na bezpečné místo. Po opuštění stránky jej nelze načíst.


Udělte registraci aplikace oprávnění používat sdílenou galerii obrázků.
1. Na webu Azure Portal vyberte Galerii sdílených bitových obrázků, kterou chcete sdílet s jiným klientem.
1. Vyberte **ovládací prvek přístupu (IAM)** a v části **Přidat přiřazení role** vyberte *Přidat*. 
1. V části **Role**vyberte **Reader**.
1. V části **Přiřadit přístup k:**, ponechte to jako **uživatele, skupinu nebo instanční objekt Služby Azure AD**.
1. V **části Select**zadejte *myGalleryApp* a vyberte ji, až se zobrazí v seznamu. Po dokončení vyberte **Uložit**.


## <a name="give-tenant-2-access"></a>Udělit klientovi 2 přístup

Podejte tenantovi 2 přístup k aplikaci vyžádáním přihlášení pomocí prohlížeče. Nahraďte * \<ID klienta2>* S ID klienta pro klienta, se kterým chcete sdílet galerii obrázků. Nahraďte * \<ID aplikace (klienta)>* ID aplikace pro registraci aplikace, kterou jste vytvořili. Po dokončení provádění nahrazení vložte adresu URL do prohlížeče a postupujte podle přihlašovacích pokynů pro přihlášení do tenanta 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

Na [portálu Azure](https://portal.azure.com) se přihlaste jako tenant 2 a udělit registraci aplikace přístup ke skupině prostředků, kde chcete vytvořit virtuální počítač.

1. Vyberte skupinu prostředků a pak vyberte **řízení přístupu (IAM)**. V části **Přidat přiřazení role** vyberte **Přidat**. 
1. V části **Role**zadejte **přispěvatel**.
1. V části **Přiřadit přístup k:**, ponechte to jako **uživatele, skupinu nebo instanční objekt Služby Azure AD**.
1. V **části Vyberte** *myGalleryApp* a vyberte ji, až se zobrazí v seznamu. Po dokončení vyberte **Uložit**.

> [!NOTE]
> Před použitím stejné spravované bitové kopie k vytvoření jiné verze bitové kopie je třeba počkat, až bude verze bitové kopie zcela dokončena.

