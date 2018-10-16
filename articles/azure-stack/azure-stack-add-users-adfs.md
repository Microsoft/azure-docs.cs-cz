---
title: Přidání uživatelů pro AD FS Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak přidat uživatele pro nasazení služby AD FS služby Azure Stack
services: azure-stack
documentationcenter: ''
author: patricka
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: patricka
ms.reviewer: unknown
ms.openlocfilehash: f8abacbcb05d1346931b5c2e1097660cfbd8e594
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344162"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Přidat uživatele Azure stacku ve službě AD FS
Můžete použít **Active Directory Users and Computers** modul snap-in pro přidání dalších uživatelů do prostředí Azure Stack využití služby AD FS jako zprostředkovatele identity.

## <a name="add-windows-server-active-directory-users"></a>Přidání uživatelů systému Windows Server Active Directory
> [!TIP]
> Tento příklad používá výchozí azurestack.local ASDK služby active directory. 

1.  Přihlaste se pomocí účtu poskytuje přístup k nástrojům pro správu Windows počítači a otevře nové Microsoft Management Console (MMC).
2.  Klikněte na tlačítko **soubor > Přidat nebo odebrat modul snap-in**.
3.  Vyberte **uživatelů služby Active Directory a počítače** > **AzureStack.local** > **uživatelé**.
4.  Klikněte na tlačítko **akce** > **nové** > **uživatele**.
5.  V tento nový objekt – uživatel zadejte a potvrďte heslo
6.  Klikněte na tlačítko **Další** finalize hodnoty a klikněte na tlačítko Dokončit vytvořte uživatele.


## <a name="next-steps"></a>Další postup
[Vytvoření instančních objektů](azure-stack-create-service-principals.md)