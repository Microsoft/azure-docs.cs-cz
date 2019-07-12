---
title: Role a požadavky na verzi Preview služby Azure Data sdílené složky
description: Role a požadavky na verzi Preview služby Azure Data sdílené složky
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: a5d70b9aa611b4f939cb46b5d25655edd818cb35
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807526"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>Role a požadavky na verzi Preview služby Azure Data sdílené složky

Tento článek popisuje role potřebné ke sdílení dat s využitím Azure sdílet náhled dat, a přijmout a přijímat data pomocí Azure Data sdílet náhled. 

## <a name="roles-and-requirements"></a>Role a požadavky

Sdílet nebo přijímat data pomocí Azure Data sdílet, musí být schopen udělit oprávnění ke sdílení dat do účtu úložiště, jsou sdílení dat z nebo příjem dat v na uživatelský účet, který používáte pro přihlášení k Azure. Obvykle se jedná oprávnění, která existuje v **vlastníka** role nebo vlastní role se stejnými Microsoft.Authorization/role přiřazení a zápis oprávnění přiřazena. 

Sdílet nebo přijímat data z nebo do účtu služby Azure Storage, musíte být vlastníkem účtu úložiště. I v případě, že jste vytvořili účet úložiště, to automaticky vám neuděluje vlastnictví účtu úložiště. Přidání uživatele do role vlastníka svého účtu Azure Storage, postupujte podle těchto kroků.

1. Přejděte na účet úložiště na webu Azure portal
1. Vyberte **řízení přístupu (IAM)**
1. Klikněte na tlačítko **přidat**
1. Přidat vás jako vlastníka

Pokud chcete zobrazit oprávnění, která máte v předplatném, na webu Azure Portal v pravém horním rohu vyberte své uživatelské jméno a potom **Oprávnění**. Pokud máte přístup k několika předplatným, vyberte odpovídající předplatné. 

## <a name="resource-provider-registration"></a>Registrace poskytovatele prostředků 

Když přijmete pozvání dat sdílené složky Azure, musíte ručně zaregistrovat poskytovatele prostředků Microsoft.DataShare v k vašemu předplatnému. Postupujte podle těchto kroků se zaregistrovat poskytovatele prostředků Microsoft.DataShare do vašeho předplatného Azure. 

1. Na webu Azure Portal, přejděte na **předplatná**
1. Vyberte předplatné, které používáte pro sdílenou složku Azure dat
1. Klikněte na **poskytovatelů prostředků**
1. Hledat Microsoft.DataShare
1. Klikněte na tlačítko **zaregistrovat**

## <a name="next-steps"></a>Další postup

- Další informace o rolích v Azure – [pochopit definice rolí](../role-based-access-control/role-definitions.md)

