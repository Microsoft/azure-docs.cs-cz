---
title: Správa aplikace Azure IoT Central | Dokumentace Microsoftu
description: Jako správce jak spravovat aplikace Azure IoT Central
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: adcc0b741cac0ac5038243c80b3da2e0ff872c95
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46466094"
---
# <a name="how-to-administer-your-application"></a>Jak spravovat aplikace

Po vytvoření aplikace Microsoft Azure IoT Central, můžete použít **správu** část Azure IoT Central uživatelské rozhraní pro jeho správu. Přejděte **správu** zvolte **správu** v levé navigační nabídce.

**Správu** část umožňuje:

- Správa uživatelů

- Správa rolí

- Zobrazit informace o fakturaci

- Spravovat nastavení aplikace

- Rozšiřte si bezplatnou zkušební verzi

V **správu** části, je sekundární navigační nabídky s odkazy na různé úlohy správy.

Přístup a používání **správu** oddíl, musí být v **správce** role v aplikaci Azure IoT Central. Pokud vytvoříte aplikaci Azure IoT Central, které se automaticky přiřazují **správce** role pro danou aplikaci. *Správa uživatelů* části v tomto článku obsahuje další informace o tom, jak přiřadit **správce** role jiným uživatelům.

## <a name="change-application-name"></a>Změna názvu aplikace

Chcete-li změnit název vaší aplikace pomocí sekundární navigační nabídce přejděte do **nastavení aplikace** stránku **správu** oddílu.

Na **nastavení aplikace** stránky, zadejte název zvoleného v **název_aplikace** pole a klikněte na tlačítko **Uložit**.

## <a name="change-the-application-url"></a>Změňte adresu URL aplikace

Změnit adresu URL pro vaši aplikaci, přejděte na pomocí sekundární navigační nabídce **nastavení aplikace** stránku **správu** oddílu.

![Stránka nastavení aplikace](media\howto-administer\image0-a.png)

Na **nastavení aplikace** stránky, zadejte adresu URL podle vašeho výběru v **URL** pole a klikněte na tlačítko **Uložit**. Vaše adresa URL může být maximálně 200 znaků. Pokud adresa URL není k dispozici, zobrazí se chyba ověření

> [!Note]
> Pokud změníte adresu URL, můžete původní adresu URL provedenou jiný zákazník Azure IoT Central. V takovém případě je již k dispozici pro použití. Když změníte adresu URL, původní adresa URL již není funkční a musí informovat uživatele o nové adrese URL použít.

## <a name="change-the-application-image"></a>Změnit image aplikací

Další informace o používání imagí v aplikaci Azure IoT Central, naleznete v tématu [Prepare a nahrávání imagí do aplikace Azure IoT Central](howto-prepare-images.md).

## <a name="copy-an-application"></a>Kopírovat aplikaci

Můžete vytvořit kopii jakékoli aplikace, bez jakékoli instance zařízení, data historie zařízení a uživatelská data. Kopie bude placené aplikace, která vám bude účtovat. Zkušební verze aplikace nejde vytvořit zkopírováním jiná aplikace.

Kopírujete aplikaci, přejděte na **nastavení aplikace** stránky a klikněte na tlačítko **kopírování** tlačítko.

![Stránka nastavení aplikace](media\howto-administer\appCopy1.png)

Kliknutím **kopírování** tlačítko se otevře dialogové okno, ve kterém můžete vybrat název, URL, AAD adresář, předplatné a oblast Azure pro novou aplikaci, která bude vytvořena tak, že zkopírujete vaší aplikace. Vyberte hodnoty pro každé z těchto polí a potom klikněte **kopírování** potvrďte, že chcete pokračovat. Další informace o tom, co zadat tyto hodnoty v článku o [jak vytvořit aplikaci](howto-create-application.md).

![Stránka nastavení aplikace](media\howto-administer\appCopy2.png)

Po úspěšném operace kopírování aplikace, budete moci přejít na novou aplikaci, která byla vytvořena tak, že zkopírujete aplikaci kliknutím na odkaz, který se zobrazí na **nastavení aplikace** stránky.

![Stránka nastavení aplikace](media\howto-administer\appCopy3.png)

> [!Note]
> Kopírování aplikace se zkopírovat definici pravidla nebo akce. Ale vzhledem k tomu, že uživatelé, kteří mají přístup k původní aplikace nejsou zkopírovány do zkopírovaný aplikace, budete mít ručně přidat uživatele do akce, jako je e-mailu, pro kterou jsou uživatelé jako nezbytné komponenty.

## <a name="delete-an-application"></a>Odstranění aplikace

Odstraňujete aplikaci, přejděte na pomocí sekundární navigační nabídce **nastavení aplikace** stránku **správu** části.

Zvolte **odstranit**.

> [!Note]
> Odstranění aplikace nevratně odstraní všechna data aplikace přidružená. Pokud chcete odstranit aplikaci, musí také mít práva k odstranění prostředků v předplatném Azure jste zvolili při vytváření aplikace. Další informace najdete v tématu [Use Role-Based řízení přístupu ke správě přístupu k prostředkům předplatného Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="roles-in-azure-iot-central"></a>Role v Azure IoT Central

Role umožňují řídit, kdo ve vaší organizaci, můžete provádět různé úlohy Azure IoT Central. Azure IoT Central má tři role, které můžete přiřazovat uživatelům vaší aplikace. Role jsou přiřazené podle jednotlivých aplikací. Stejný uživatel může mít různé role v různých aplikacích. Stejný uživatel může můžete přiřadit k více rolím v rámci aplikace.

### <a name="administrator"></a>Správce

Uživatelé v **správce** role mají přístup ke všem funkcím v aplikaci Azure IoT Central.

Uživatel vytvářející aplikace se automaticky přiřadí **správce** role. Musí být vždy alespoň jeden uživatel ve **správce** role.

### <a name="application-builder"></a>Tvůrce aplikací

Uživatelé v **Tvůrce aplikací** role můžou provádět vše v aplikaci Azure IoT Central, s výjimkou spravovat aplikace.

### <a name="application-operator"></a>Aplikace – operátor

Uživatelé v **operátor aplikací** roli nemají přístup k **Tvůrce aplikací** stránky. Nelze spravovat aplikace.

## <a name="manage-users"></a>Správa uživatelů

Správci aplikace můžete přiřadit uživatele k rolím v aplikaci.

### <a name="add-users"></a>Přidání uživatelů

Než mohou přihlásit a získat přístup k Azure IoT Central aplikaci, každý uživatel musí mít uživatelský účet. Accounts Microsoft (MSA) a účty Azure Active Directory (AAD) jsou podporované v Azure IoT Central. Skupiny Azure Active Directory nejsou aktuálně podporované v Azure IoT Central.

Další informace najdete v tématu [nápovědy účtu společnosti Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) a [rychlý start: přidání nových uživatelů do služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Přidat uživatelský účet k aplikaci Azure IoT Central, použijte sekundární navigační nabídky přejít na **uživatelé** stránku **správu** oddílu.

    ![Seznam uživatelů](media\howto-administer\image1.png)

1. Na **uživatelé** zvolte **+ přidat uživatele** přidání uživatele.

    ![Přidání uživatele](media\howto-administer\image2.png)

1. Při přidání uživatele do aplikace Azure IoT Central, vyberte roli pro uživatele **Role** rozevíracího seznamu. Další informace o rolích v *role v Azure IoT Central* části tohoto článku.

    ![Výběr role](media\howto-administer\image3.png)

    > [!NOTE]
    >  Hromadné přidání uživatelů, zadejte ID uživatele ze všech uživatelů, které chcete přidat oddělených středníky. Zvolte roli z **Role** rozevírací seznam a zvolte **Uložit**.

1. Poté, co přidáte uživatele, zobrazí se položka pro tohoto uživatele na **uživatelé** stránky.

    ![Seznam uživatelů](media\howto-administer\image4.png)

### <a name="edit-the-roles-assigned-to-users"></a>Upravit role přiřazené uživateli

Role nelze změnit jednou assinged. Chcete-li změnit role přiřazené uživateli, odstraňte uživatele a přidejte znovu s jinou rolí uživatele.

### <a name="delete-users"></a>Odstranit uživatele

Chcete-li odstranit uživatele, podívejte se na jeden nebo více políček **uživatelé** stránce a pak zvolte **odstranit**.

## <a name="view-your-bill"></a>Prohlédněte si fakturu

Zobrazte svoji fakturu, přejděte na **fakturace** stránku **správu** části a zvolte **fakturace**. Na nové kartě se otevře Azure fakturační stránku a na faktuře uvidíte pro jednotlivé aplikace Azure IoT Central.

## <a name="convert-your-trial-to-a-paid-application"></a>Převést na placené aplikace platnost svojí zkušební verze

Jakmile jste vyhodnotili IoT Central, můžete převést platnost svojí zkušební verze na placené aplikace. K dokončení tohoto procesu samoobslužné služby, postupujte podle těchto kroků:

1. Pomocí sekundární navigační nabídce přejděte do **fakturace** stránku **správu** oddílu. Pokud jste nerozšířili platnost svojí zkušební verze, bude stránka vypadat nějak takto:

    ![Bezplatná zkušební verze stavu](media/howto-administer/freetrial.png)

2. Klikněte na tlačítko **převést na placené**. Pokud jste nerozšířili platnost svojí zkušební verze, automaticky otevírané okno vypadá takto:

    V místní nabídce vyberte odpovídající tenanta Azure Active Directory a předplatné Azure, který chcete použít pro vaše aplikace IoT Central.

    ![Prodlužte platnost bezplatné zkušební verze](media/howto-administer/extend.png)

3. Po kliknutí na **převést**, platnost svojí zkušební verze se převede na placené aplikace a spustit načítání účtuje.

## <a name="extend-your-free-trial"></a>Prodloužit platnost bezplatné zkušební verze

Ve výchozím nastavení všechny bezplatné zkušební verze jsou k dispozici po dobu 7 dní. Pokud chcete zvýšit vaše zkušební verze na 30 dnů, postupujte takto:

1. Pomocí sekundární navigační nabídce přejděte do **fakturace** stránku **správu** části:

1. Klikněte na tlačítko **prodloužit platnost zkušební verze**. V místní nabídce vyberte odpovídající tenanta Azure Active Directory a potom předplatné Azure pro vaše aplikace IoT Central:

1. Pak klikněte na tlačítko **rozšířit**. Platnost svojí zkušební verze je teď platný po dobu 30 dnů.

## <a name="utilize-the-azure-sdks-to-do-control-plane-operations"></a>Využití sady Azure SDK pro operace roviny řízení.

IoT Central Azure Resource Manageru SDK balíčky jsou k dispozici pro uzel, Python, C#, Ruby, Java a Go. Tyto knihovny podpory operace roviny řízení pro IoT Central, což umožňuje vytváření, výpis, aktualizovat nebo odstranit aplikace IoT Central. Obsahují taky pomocné rutiny pro práci s ověřováním a Chyba zpracování, která je specifická pro jednotlivé jazyky. 

Příklady použití sady SDK Azure Resource Manageru najdete v [ https://github.com/emgarten/iotcentral-arm-sdk-examples ](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Další informace podívejte se na tyto balíčky na Githubu.

| Jazyk | Úložiště | Balíček |
| ---------| ---------- | ------- |
| Node | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Přejít | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak spravovat aplikace Azure IoT Central, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Nastavení šablony zařízení](howto-set-up-template.md)