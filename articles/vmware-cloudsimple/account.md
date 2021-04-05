---
title: Správa účtů – Azure VMware Solution by CloudSimple Portal
description: Popisuje, jak spravovat účty na řešení Azure VMware pomocí portálu CloudSimple.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4c26d5accce77ce6fd8c9b6c2b519b93f95013ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895168"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>Správa účtů na řešení Azure VMware pomocí portálu CloudSimple

Při vytváření služby CloudSimple se vytvoří účet na CloudSimple. Účet je přidružený k vašemu předplatnému Azure, kde se služba nachází. Všichni uživatelé s rolemi vlastník a přispěvatel v předplatném mají přístup k portálu CloudSimple. ID předplatného Azure a ID tenanta přidružené ke službě CloudSimple najdete na stránce účty.

Pokud chcete spravovat účty na portálu CloudSimple, [přejděte na portál](access-cloudsimple-portal.md) a v postranní nabídce vyberte **účet** .

Výběrem **souhrnu** zobrazíte informace o konfiguraci CloudSimple vaší společnosti. Zobrazí se aktuální kapacita vaší konfigurace cloudu, včetně počtu privátních cloudů, celkového úložiště, konfigurace clusteru vSphere, počtu uzlů a počtu výpočetních jader. Odkaz je zahrnutý k nákupu dalších uzlů, pokud aktuální konfigurace nevyhovuje všem vašim potřebám.

## <a name="email-alerts"></a>E-mailová upozornění

Můžete přidat e-mailové adresy všech osob, které chcete upozornit na změny konfigurace privátního cloudu.

1. V oblasti **Další e-mailová upozornění** klikněte na **Přidat nový**.
2. Zadejte e-mailovou adresu.
3. Stiskněte tlačítko vrátit.  

Pokud chcete položku odebrat, klikněte na **X**.

## <a name="cloudsimple-operator-access"></a>Přístup k operátoru CloudSimple

Nastavení přístupu operátora umožňuje CloudSimple pomoci při řešení potíží tím, že se pracovník podpory může přihlásit ke svému portálu CloudSimple.  Toto nastavení je ve výchozím nastavení povolené. Všechny akce prováděné pracovníkem podpory, když se přihlásíte ke svému účtu zákazníka, se zaznamenávají a jsou k dispozici pro vaši kontrolu na  >  stránce **audit** aktivity.

Kliknutím na přepínač **Povolit přístup k operátoru CloudSimple** zapněte nebo vypněte přístup.
