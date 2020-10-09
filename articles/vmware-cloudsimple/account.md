---
title: Správa účtů – Azure VMware Solution by CloudSimple Portal
description: Popisuje, jak spravovat účty na řešení Azure VMware pomocí portálu CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77025363"
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

Nastavení přístupu operátora umožňuje CloudSimple pomoci při řešení potíží tím, že se pracovník podpory může přihlásit ke svému portálu CloudSimple.  Toto nastavení je ve výchozím nastavení povolené. Všechny akce prováděné pracovníkem podpory, když se přihlásíte ke svému účtu zákazníka, se zaznamenávají a jsou k **Activity**dispozici pro vaši kontrolu na  >  stránce**audit** aktivity.

Kliknutím na přepínač **Povolit přístup k operátoru CloudSimple** zapněte nebo vypněte přístup.
