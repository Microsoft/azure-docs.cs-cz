---
title: Kurz – odstranění privátního cloudu řešení Azure VMware
description: Naučte se, jak odstranit privátní cloud řešení Azure VMware, který už nepotřebujete.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 453e7a3316c342cd724a951eafea0ae9fa045506
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462095"
---
# <a name="tutorial-delete-an-azure-vmware-solution-private-cloud"></a>Kurz: odstranění privátního cloudu řešení Azure VMware

Pokud máte privátní cloud řešení Azure VMware, který už nepotřebujete, můžete ho odstranit. Privátní cloud zahrnuje izolovanou síťovou doménu, jeden nebo více zřízených clusterů vSphere na vyhrazených hostitelích serverů a několik virtuálních počítačů (VM). Při odstranění privátního cloudu dojde k odstranění všech virtuálních počítačů, jejich dat a clusterů. Vyhrazení hostitelé řešení Azure VMware se bezpečně vymažou a vrátí do bezplatného fondu. Odstraněné síťové adresní místo je také odstraněno.  

> [!CAUTION]
> Odstranění privátního cloudu je nevratná operace. Po odstranění privátního cloudu se data nedají obnovit, protože ukončí všechny spuštěné úlohy a komponenty a zničí všechna data privátního cloudu a nastavení konfigurace, včetně veřejných IP adres.

## <a name="prerequisites"></a>Požadavky

Pokud budete vyžadovat virtuální počítače a jejich data později, nezapomeňte před odstraněním privátního cloudu zálohovat data.  Neexistuje žádný způsob, jak obnovit virtuální počítače a jejich data.


## <a name="delete-the-private-cloud"></a>Odstranění privátního cloudu

1. Přístup ke konzole řešení Azure VMware v [Azure Portal](https://portal.azure.com).

2. Vyberte privátní cloud, který chcete odstranit.
 
3. Zadejte název privátního cloudu a vyberte **Ano**. 

>[!NOTE]
>Dokončení procesu odstraňování trvá několik hodin.  
