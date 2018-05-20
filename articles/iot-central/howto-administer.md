---
title: Spravovat Azure IoT centrální aplikaci | Microsoft Docs
description: Jako správce Správa Azure IoT centrální aplikace
services: iot-central
author: TanmayBhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: b60b9e851a3b6612964e67e7764ad8d43d606b4e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="how-to-administer-your-application"></a>Postupy při správě vaší aplikace

Po vytvoření Microsoft Azure IoT centrální aplikaci, můžete použít **správy** části Azure IoT centrální uživatelského rozhraní pro jeho správa. Přejděte na **správy** zvolte **správy** v levé navigační nabídce.

**Správy** oddílu můžete:

- Správa uživatelů

- Správa rolí

- Zobrazit fakturační informace

- Spravovat nastavení aplikace

- Rozšíření bezplatné zkušební verze

V **správy** části, je sekundární navigační nabídky s odkazy na různé úlohy správy.

Pro přístup a použít **správy** oddíl, musí být v **správce** role v Azure IoT centrální aplikaci. Pokud vytvoříte aplikaci Azure IoT centrální, můžete se automaticky přiřazují do **správce** role pro danou aplikaci. *Správa uživatelů* v tomto článku vysvětluje více o tom, jak přiřadit **správce** role ostatním uživatelům.

## <a name="change-application-name"></a>Změna názvu aplikace

Chcete-li změnit název vaší aplikace, použijte sekundární navigační nabídce přejděte na **nastavení aplikace** stránky v **správy** části.

Na **nastavení aplikace** stránky, zadejte název podle své volby v **název aplikace** pole a potom vyberte **Uložit**.

## <a name="change-the-application-url"></a>Změňte adresu URL aplikace

Chcete-li změnit adresu URL pro aplikaci, použijte sekundární navigační nabídce přejděte na **nastavení aplikace** stránky v **správy** části.

![Stránky nastavení aplikace](media\howto-administer\image0-a.png)

Na **nastavení aplikace** stránky, zadejte adresu URL vlastního výběru v **URL** pole a potom vyberte **Uložit**. Adresa URL může být maximálně 200 znaků. Pokud adresa URL není k dispozici, zobrazí chybu ověření

> [!Note]
> Pokud změníte adresu URL, můžete původní adresu URL provedenou jinou Azure IoT centrální zákazníka. V takovém případě je již k dispozici pro použití. Když změníte adresu URL, starý URL přestane fungovat a musíte upozornit uživatele o nové adrese URL používat.

## <a name="change-the-application-image"></a>Změna bitové kopie aplikace

Další informace o použití obrázků v aplikaci Azure IoT centrální najdete v tématu [Příprava a nahrajte Image aplikace Azure IoT centrální](howto-prepare-images.md).

## <a name="delete-an-application"></a>Odstranit aplikaci

Pokud chcete odstranit aplikaci, použijte sekundární navigační nabídce přejděte na **nastavení aplikace** stránku **správy** části.

Zvolte **odstranit**.

> [!Note]
> Odstranění aplikace nevratně odstraní všechna data související s aplikací. Chcete-li aplikaci odstranit, je také nutné mít práva k odstranění prostředků v rámci předplatného Azure jste zvolili při vytváření aplikace. Další informace najdete v tématu [Use Role-Based řízení přístupu ke správě přístupu k prostředkům předplatného Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="roles-in-azure-iot-central"></a>Role v Azure IoT – střed

Role umožňují určit, kdo v rámci vaší organizace, můžete provádět různé úlohy Azure IoT centrální. Azure IoT střed má tři role, které je možné přiřadit uživatelům vaší aplikace. Role jsou přiřazeny každou aplikaci. Stejný uživatel může mít různé role v různých aplikací. Stejný uživatel může můžete přiřadit k více rolím v rámci aplikace.

### <a name="administrator"></a>Správce

Uživatelé v **správce** role mají přístup ke všem funkcím v Azure IoT centrální aplikaci.

Uživatel vytváření aplikací se automaticky přiřadí k **správce** role. Musí být vždy alespoň jednoho uživatele **správce** role.

### <a name="application-builder"></a>Tvůrce aplikací

Uživatelé v **Tvůrce aplikací** role může dělat všechno, v aplikaci Azure IoT centrální s výjimkou spravovat aplikace.

### <a name="application-operator"></a>Operátor aplikací

Uživatelé v **operátor aplikací** roli nemají přístup k **Tvůrce aplikací** stránky. Se nemohou spravovat aplikaci.

## <a name="manage-users"></a>Správa uživatelů

Správci aplikace můžete přiřadit uživatele k rolím v aplikaci.

### <a name="add-users"></a>Přidání uživatelů

Každý uživatel musí mít uživatelský účet, předtím, než může přihlásit a získat přístup k Azure IoT centrální aplikaci. V Azure IoT centrální jsou podporované Accounts Microsoft (MSA) a účty Azure Active Directory (AAD). Skupiny Azure Active Directory nejsou aktuálně podporované v centrální Azure IoT.

Další informace najdete v tématu [nápovědy společnosti Microsoft účet](https://support.microsoft.com/products/microsoft-account?category=manage-account) a [rychlé spuštění: přidání nových uživatelů do služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. K přidání uživatelského účtu do Azure IoT centrální aplikaci, použijte sekundární navigační nabídce přejděte na **uživatelé** stránku **správy** části.

    ![Seznam uživatelů](media\howto-administer\image1.png)

1. Na **uživatelé** vyberte **+ přidat uživatele** přidat uživatele.

    ![Přidání uživatele](media\howto-administer\image2.png)

1. Při přidávání uživatele do aplikace Azure IoT centrální, vyberte roli uživatele z **Role** rozevíracího seznamu. Další informace o rolích v *role v Azure IoT centrální* tohoto článku.

    ![Výběr role](media\howto-administer\image3.png)

    > [!NOTE]
    >  Hromadné přidání uživatelů, zadejte ID uživatele všech uživatelů, které chcete přidat oddělených středníky. Vyberte roli z **Role** rozevíracího seznamu a vyberte **Uložit**.

1. Až přidáte uživatele, zobrazí se položka pro tohoto uživatele na **uživatelé** stránky.

    ![Seznam uživatelů](media\howto-administer\image4.png)

### <a name="edit-the-roles-assigned-to-users"></a>Upravit role přiřazené k uživatelům

Role nelze změnit po assinged. Chcete-li změnit role přiřazené uživateli, odstraňte uživatele a přidejte znovu s jinou rolí uživatele.

### <a name="delete-users"></a>Odstranit uživatele

Odstranit uživatele, zkontrolujte na jeden nebo více políček **uživatelé** stránce a pak vyberte **odstranit**.

## <a name="view-your-bill"></a>Zobrazit vaše faktura

Chcete-li zobrazit vaše faktura, přejděte na **fakturace** stránky v **správy** tématu a zvolte **fakturace**. Stránky Azure fakturace otevře novou záložku a zobrazí se faktuře pro každou z Azure IoT centrální aplikací.

## <a name="convert-your-trial-to-a-paid-application"></a>Zkušební verzi převést na placené aplikaci

Jakmile jste vyhodnotit střed IoT, můžete převést zkušební období služby placené aplikace. K dokončení tohoto procesu samoobslužné služby, postupujte takto:

1. Použitím sekundární navigační nabídce přejděte **fakturace** stránky v **správy** části. Pokud jste nerozšířili vaše zkušební období, stránce vypadá takto:

    ![Bezplatná zkušební verze stavu](media/howto-administer/freetrial.png)

2. Klikněte na tlačítko **převést na placené**. Pokud jste nerozšířili vaše zkušební období, místní nabídce vypadá takto:

    V místní nabídce vyberte odpovídající klienta Azure Active Directory a potom předplatné Azure, které chcete použít pro aplikaci Centrální IoT.

    ![Rozšíření bezplatné zkušební verze](media/howto-administer/extend.png)

3. Po kliknutí na tlačítko **převést**, vaše zkušební období je převést na placené aplikace a spustit získávání účtují.

## <a name="extend-your-free-trial"></a>Bezplatnou zkušební verzi prodloužit

Ve výchozím nastavení všechny bezplatné zkušební verze jsou k dispozici po dobu 7 dnů. Pokud chcete zvýšit si zkušební verzi na 30 dní, postupujte podle těchto kroků:

1. Použitím sekundární navigační nabídce přejděte **fakturace** stránky v **správy** části:

1. Klikněte na tlačítko **zkušební verzi prodloužit**. V místní nabídce vyberte odpovídající klienta Azure Active Directory a poté předplatné Azure pro aplikaci Centrální IoT:

1. Pak klikněte na tlačítko **rozšíření**. Zkušební období služby je nyní platný po dobu 30 dnů.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili postupy při správě aplikace Azure IoT centrální, zde je navržené další krok:

> [!div class="nextstepaction"]
> [Nastavení šablony zařízení](howto-set-up-template.md)