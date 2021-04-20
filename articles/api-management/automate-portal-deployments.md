---
title: Automatizace nasazení portálu pro vývojáře
titleSuffix: Azure API Management
description: Přečtěte si, jak automaticky migrovat obsah samoobslužného portálu pro vývojáře mezi dvěma API Management službami.
author: erikadoyle
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: e189a9339f6ca3bc81148b86206ddd052392074f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741637"
---
# <a name="automate-developer-portal-deployments"></a>Automatizace nasazení portálu pro vývojáře

Portál pro vývojáře API Management podporuje programový přístup k obsahu. Umožňuje importovat nebo exportovat data z API Management služby prostřednictvím [REST API správy obsahu](/rest/api/apimanagement/). Přístup k REST API funguje pro spravované i samoobslužné portály.

## <a name="automated-migration-script"></a>Skript automatizované migrace

Rozhraní API můžete použít k automatizaci migrace obsahu mezi dvěma API Management službami – například službu v testovacím prostředí a službu v produkčním prostředí. `scripts.v3/migrate.js`Tento skript usnadňuje tento proces automatizace pomocí skriptu v [úložišti GitHub](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v3/migrate.js) portálu pro vývojáře API Management.

> [!WARNING]
> Skript odebere obsah portálu pro vývojáře ve vaší cílové API Management službě. Pokud se o ni zajímáte, ujistěte se, že jste provedli zálohování.

> [!NOTE]
> Pokud používáte Samoobslužný portál s explicitně definovaným vlastním účtem úložiště pro hostování mediálních souborů (tj. definujete `blobStorageUrl` nastavení v `config.design.json` konfiguračním souboru), musíte použít původní `scripts/migrate.js` [skript](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v2/migrate.js). Původní skript nefunguje pro spravované nebo samoobslužné portály s účtem úložiště médií spravovaným nástrojem API Management. V takovém případě použijte místo toho skript ze `/scripts.v3` složky.

Skript provede následující kroky:

1. Zachyťte obsah a médium portálu ze zdrojové API Management služby.
1. Odeberte obsah a médium portálu z cílové API Management služby.
1. Nahrajte obsah portálu a médium do cílové API Management služby.
1. Volitelně a jenom pro spravované portály – portál automaticky publikuje.

Po úspěšném spuštění skriptu by cílová služba API Management měla obsahovat stejný obsah portálu jako zdrojová služba a Vy ho budete moct zobrazit jako správce.

* Pokud používáte spravovaný portál, můžete nastavit skript tak, aby automaticky publikoval cílový portál, aby bylo migrované verze automaticky dostupné pro návštěvníky. 
* Pokud používáte Samoobslužný portál, musíte cílový portál publikovat ručně. Postupujte podle pokynů pro publikování a hostování v tomto kurzu a [nastavte portál pro vývojáře na samoobslužném portálu](developer-portal-self-host.md).

## <a name="next-steps"></a>Další kroky

Další informace o portálu pro vývojáře:

- [Přehled vývojářského portálu pro službu Azure API Management](api-management-howto-developer-portal.md)
- [Samoobslužný hostování portálu pro vývojáře](developer-portal-self-host.md)