---
title: Aktualizovat heslo účtu úložiště služba AD DS
description: Naučte se aktualizovat heslo účtu Active Directory Domain Services, který představuje váš účet úložiště. Tím se zabrání vyčištění účtu úložiště, když vyprší platnost hesla, což brání selhání ověřování.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: rogarana
ms.openlocfilehash: 936580f19d7370f72ebdc52cb39f9f5e1cb5984d
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84268454"
---
# <a name="update-the-password-of-your-storage-account-identity-in-ad-ds"></a>Aktualizujte heslo identity účtu úložiště v služba AD DS

Pokud jste zaregistrovali identitu nebo účet Active Directory Domain Services (služba AD DS), který představuje váš účet úložiště v organizační jednotce nebo doméně, která vystavuje čas vypršení platnosti hesla, musíte změnit heslo před maximálním stářím hesla. Vaše organizace může spouštět skripty automatického čištění, které po vypršení platnosti hesla odstraňují účty. Z tohoto důvodu platí, že pokud heslo před vypršením platnosti nebudete měnit, mohli byste účet odstranit, což způsobí ztrátu přístupu ke sdíleným složkám souborů Azure.

Pokud chcete aktivovat otočení hesla, můžete spustit `Update-AzStorageAccountADObjectPassword` příkaz z [modulu AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases). Tento příkaz se musí spustit v místním prostředí s služba AD DS připojeném pomocí hybridního uživatele s oprávněním vlastníka účtu úložiště a služba AD DS oprávnění ke změně hesla identity představující účet úložiště. Příkaz provede akce podobné rotaci klíče účtu úložiště. Konkrétně získá druhý klíč protokolu Kerberos účtu úložiště a použije ho k aktualizaci hesla registrovaného účtu v služba AD DS. Pak znovu vygeneruje cílový klíč protokolu Kerberos účtu úložiště a aktualizuje heslo zaregistrovaného účtu v služba AD DS. Tento příkaz musíte spustit v místním prostředí připojeném služba AD DS.

```PowerShell
# Update the password of the AD DS account registered for the storage account
# You may use either kerb1 or kerb2
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```
