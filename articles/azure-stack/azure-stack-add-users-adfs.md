---
title: Přidání uživatelů pro AD FS Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak přidat uživatele pro nasazení služby AD FS služby Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 1b47739200c79317273ea0c788f21a7ee4a3b818
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648502"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Přidat uživatele Azure stacku ve službě AD FS
Můžete použít **Active Directory Users and Computers** modul snap-in pro přidání dalších uživatelů do prostředí Azure Stack využití služby AD FS jako zprostředkovatele identity.

## <a name="add-windows-server-active-directory-users"></a>Přidání uživatelů systému Windows Server Active Directory
> [!TIP]
> Tento příklad používá výchozí azurestack.local ASDK služby active directory. 

1. Přihlaste se pomocí účtu poskytuje přístup k nástrojům pro správu Windows počítači a otevře nové Microsoft Management Console (MMC).
2. Vyberte **soubor > Přidat nebo odebrat modul snap-in**.
3. Vyberte **uživatelů služby Active Directory a počítače** > **AzureStack.local** > **uživatelé**.
4. Vyberte **akce** > **nové** > **uživatele**.
5. Nový objekt – uživatel zadejte podrobné informace o uživateli. Vyberte **Další**.
6. Zadejte a potvrďte heslo.
7. Vyberte **Další** pro dokončení hodnoty. Vyberte **Dokončit** pro vytvoření uživatele.


## <a name="next-steps"></a>Další postup
[Vytvoření instančních objektů](azure-stack-create-service-principals.md)