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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "73903708"
---
Pokud ale chcete sdílet image mimo vašeho tenanta Azure, měli byste při škálování vytvořit registraci aplikace, která usnadňuje sdílení.  Použití registrace aplikace může umožnit složitější scénáře sdílení, třeba: 

* Správa sdílených imagí, když jedna společnost získá jiný a infrastruktura Azure se rozprostře mezi samostatné klienty. 
* Partneři Azure spravují jménem svých zákazníků infrastrukturu Azure. Přizpůsobení imagí se provádí v tenantovi partnerů, ale k nasazení infrastruktury dojde v tenantovi zákazníka. 


## <a name="create-the-app-registration"></a>Vytvoření registrace aplikace

Vytvořte registraci aplikace, kterou budou používat oba klienti ke sdílení prostředků Galerie imagí.
1. Otevřete [Registrace aplikací (Preview) v Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. V nabídce v horní části stránky vyberte **Nová registrace** .
1. Do **název**zadejte *myGalleryApp*.
1. V **podporovaných typech účtů**vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft**.
1. V seznamu **identifikátor URI pro přesměrování**zadejte *https://www.microsoft.com* a pak vyberte **Registrovat**. Po vytvoření registrace aplikace se otevře stránka s přehledem.
1. Na stránce Přehled zkopírujte **ID aplikace (klienta)** a uložte se pro použití později.   
1. Vyberte **certifikáty & tajných**kódů a pak vyberte **nový tajný klíč klienta**.
1. V **popisu**zadejte do *Galerie sdílených imagí cloudový klíč aplikace pro více tenantů*.
1. V poli **konec platnosti**ponechte výchozí hodnotu **v intervalu 1 roku** a pak vyberte **Přidat**.
1. Zkopírujte hodnotu tajného klíče a uložte ji na bezpečné místo. Po opuštění stránky ji nelze načíst.


Udělte registraci aplikace oprávnění používat galerii sdílených imagí.
1. V Azure Portal vyberte galerii sdílených imagí, kterou chcete sdílet s jiným klientem.
1. Vyberte **Vybrat řízení přístupu (IAM)** a v části **Přidat přiřazení role** vyberte *Přidat*. 
1. V části **role**vyberte **Čtenář**.
1. V části **přiřadit přístup k:** ponechte toto jako **uživatel, skupina nebo INSTANČNÍ objekt Azure AD**.
1. V části **Vybrat**zadejte *myGalleryApp* a při zobrazení v seznamu ho vyberte. Po dokončení vyberte **Uložit**.


## <a name="give-tenant-2-access"></a>Udělit Tenantovi 2 přístup

Udělte Tenantovi 2 přístup k aplikaci tím, že požádá o přihlášení pomocí prohlížeče. Nahraďte *\<Tenant2 ID>* ID tenanta pro tenanta, se kterým chcete galerii imagí sdílet. Nahraďte *\<Application (client) ID>* ID aplikace, kterou jste vytvořili pro registraci aplikace. Až změny dokončíte, vložte adresu URL do prohlížeče a postupujte podle výzev k přihlášení a přihlaste se ke klientovi 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

V [Azure Portal](https://portal.azure.com) Přihlaste se jako tenant 2 a udělte registraci aplikace přístup ke skupině prostředků, ve které chcete vytvořit virtuální počítač.

1. Vyberte skupinu prostředků a pak vyberte **řízení přístupu (IAM)**. V části **Přidat přiřazení role** vyberte **Přidat**. 
1. V části **role**zadejte **Přispěvatel**.
1. V části **přiřadit přístup k:** ponechte toto jako **uživatel, skupina nebo INSTANČNÍ objekt Azure AD**.
1. V části **Vybrat** typ *myGalleryApp* vyberte, když se zobrazí v seznamu. Po dokončení vyberte **Uložit**.

> [!NOTE]
> Aby bylo možné použít stejnou spravovanou bitovou kopii k vytvoření jiné verze bitové kopie, je třeba počkat na dokončení sestavení a repliky verze image.

