---
title: Správa účtů – portál Azure VMware Solutions (AVS)
description: Popisuje, jak spravovat účty na portálu Azure VMware Solutions (AVS).
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1fd7cb1c55fb664828448cef0b67ea9b16323bdf
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025363"
---
# <a name="manage-accounts-on-the-azure-vmware-solutions-avs-portal"></a>Správa účtů na portálu Azure VMware Solutions (AVS)

Když vytvoříte službu AVS, vytvoří účet na službě AVS. Účet je přidružený k vašemu předplatnému Azure, kde se služba nachází. Všichni uživatelé s rolemi vlastník a přispěvatel v předplatném mají přístup k portálu AVS. ID předplatného Azure a ID tenanta přidružené ke službě AVS najdete na stránce účty.

Pokud chcete spravovat účty na portálu pro funkci AVS, [přejděte na portál](access-cloudsimple-portal.md) a v postranní nabídce vyberte **účet** .

Výběrem **souhrnu** zobrazíte informace o konfiguraci služby AVS vaší společnosti. Zobrazí se aktuální kapacita vaší konfigurace cloudu, včetně počtu privátních cloudů služby AVS, celkového úložiště, konfigurace clusteru vSphere, počtu uzlů a počtu výpočetních jader. Odkaz je zahrnutý k nákupu dalších uzlů, pokud aktuální konfigurace nevyhovuje všem vašim potřebám.

## <a name="email-alerts"></a>E-mailové výstrahy

Můžete přidat e-mailové adresy všech osob, které byste chtěli upozornit na změny v konfiguraci privátního cloudu pro funkci AVS.

1. V oblasti **Další e-mailová upozornění** klikněte na **Přidat nový**.
2. Zadejte e-mailovou adresu.
3. Stiskněte tlačítko vrátit.  

Pokud chcete položku odebrat, klikněte na **X**.

## <a name="avs-operator-access"></a>Přístup k operátoru AVS

Nastavení přístupu operátora umožňuje službě AVS vám pomoci při řešení potíží tím, že umožňuje pracovníkovi podpory přihlašovat se k portálu služby AVS. Toto nastavení je ve výchozím nastavení povolené. Všechny akce prováděné pracovníkem podpory, když se přihlásíte ke svému účtu zákazníka, se zaznamenávají a jsou k dispozici pro vaši kontrolu na stránce pro **audit** **aktivity** > .

Kliknutím na přepínač **Povolit přístup k operátoru AVS** zapněte nebo vypněte přístup.
