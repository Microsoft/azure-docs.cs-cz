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
ms.openlocfilehash: 1bb0bc0aa7ad6bbbad502832ba8e0a96f36de428
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268294"
---
# <a name="administer-your-iot-central-application"></a>Spravovat aplikace IOT Central

Po vytvoření aplikace Microsoft Azure IoT Central, můžete použít **správu** část Azure IoT Central uživatelské rozhraní pro jeho správu. Přejděte **správu** vyberte **správu** v levé navigační nabídce.

**Správu** část umožňuje:

- Správa uživatelů

- Správa rolí

- Zobrazit informace o fakturaci

- Spravovat nastavení aplikace

- Nabízí bezplatné zkušební verze

V **správu** část, sekundární navigační nabídka obsahuje odkazy na různé úlohy správy.

Pro přístup k a použít **správu** oddíl, musí být v **správce** role pro aplikaci Azure IoT Central. Pokud vytvoříte aplikaci Azure IoT Central, je automaticky přiřazen k **správce** role pro danou aplikaci. *Správa uživatelů* části v tomto článku obsahuje další informace o tom, jak přiřadit **správce** role jiným uživatelům.

## <a name="change-application-name"></a>Změna názvu aplikace

Chcete-li změnit název vaší aplikace, přejděte na pomocí sekundární navigační nabídce **nastavení aplikace** stránku **správu** části.

Na **nastavení aplikace** stránky, zadejte název zvoleného v **název_aplikace** pole. Potom vyberte **Uložit**.

## <a name="change-the-application-url"></a>Změňte adresu URL aplikace

Změnit adresu URL pro vaši aplikaci, přejděte na pomocí sekundární navigační nabídce **nastavení aplikace** stránku **správu** oddílu.

![Stránka nastavení aplikace](media\howto-administer\image0-a.png)

Na **nastavení aplikace** stránky, zadejte adresu URL podle vašeho výběru v **URL** pole a pak vyberte **Uložit**. Vaše adresa URL může být maximálně 200 znaků. Pokud adresa URL není k dispozici, se zobrazí chyba ověření.

> [!Note]
> Pokud změníte adresu URL, můžete původní adresu URL provedenou jiný zákazník Azure IoT Central. Pokud k tomu dojde, není již k dispozici pro použití. Když změníte adresu URL, původní adresa URL již není funkční a budete muset informovat uživatele o nové adrese URL použít.

## <a name="change-the-application-image"></a>Změnit image aplikací

Další informace o používání imagí v aplikaci Azure IoT Central, naleznete v tématu [Prepare a nahrávání imagí do aplikace Azure IoT Central](howto-prepare-images.md).

## <a name="copy-an-application"></a>Kopírovat aplikaci

Můžete vytvořit kopii jakékoli aplikace, bez jakékoli instance zařízení, data historie zařízení a uživatelská data. Kopie bude placené aplikace, která vám budeme účtovat. Zkušební verze aplikace nejde vytvořit zkopírováním jiná aplikace.

Kopírujete aplikaci, přejděte **nastavení aplikace** stránky. Vyberte **kopírování** tlačítko.

![Stránka nastavení aplikace](media\howto-administer\appCopy1.png)

Výběr **kopírování** tlačítko otevře dialogové okno, ve kterém můžete vybrat název, URL, Azure AD directory, předplatné a oblast Azure pro novou aplikaci, která bude vytvořena tak, že zkopírujete vaší aplikace. Vyberte hodnoty pro každé z těchto polí. Vyberte **kopírování** potvrďte, že chcete pokračovat. Další informace o tom, co zadat tyto hodnoty v tomto článku o [jak vytvořit aplikaci](howto-create-application.md).

![Stránka nastavení aplikace](media\howto-administer\appCopy2.png)

Po úspěšném provedení operace kopie aplikace, můžete přejít na novou aplikaci, která byla vytvořena tak, že zkopírujete vaší aplikace. Chcete-li přejít do aplikace, vyberte odkaz, který se zobrazí na **nastavení aplikace** stránky.

![Stránka nastavení aplikace](media\howto-administer\appCopy3.png)

> [!Note]
> Kopírování aplikace také zkopíruje definici pravidla nebo akce. Ale vzhledem k tomu, že uživatelé, kteří mají přístup k původní aplikace nejsou zkopírovány do zkopírovaný aplikace, budete muset ručně přidat uživatele do akce, jako je e-mailu, pro kterou jsou uživatelé jako nezbytné komponenty.

## <a name="delete-an-application"></a>Odstranění aplikace

Odstraňujete aplikaci, přejděte na pomocí sekundární navigační nabídce **nastavení aplikace** stránku **správu** části.

Zvolte **odstranit**.

> [!Note]
> Odstraňuje se aplikace trvale odstraní všechna data, která má přidruženou k této aplikaci.  Aplikaci odstranit, musíte také mít oprávnění k odstranění prostředků v předplatném Azure jste zvolili při vytváření aplikace. Další informace najdete v tématu [použití řízení přístupu na základě rolí ke správě přístupu k prostředkům předplatného Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="roles-in-azure-iot-central"></a>Role v Azure IoT Central

Role umožňují řídit, kdo ve vaší organizaci můžete provádět různé úlohy Azure IoT Central. Azure IoT Central má tři role, které můžete přiřazovat uživatelům vaší aplikace. Role jsou přiřazené podle jednotlivých aplikací. Stejný uživatel může mít různé role v různých aplikacích. Stejný uživatel můžete přiřadit k více rolím v rámci aplikace.

### <a name="administrator"></a>Správce

Uživatelé v **správce** role mají přístup ke všem funkcím v aplikaci Azure IoT Central.

Uživatel, který vytváří aplikace se automaticky přiřadí **správce** role. Musí být vždy alespoň jeden uživatel ve **správce** role.

### <a name="application-builder"></a>Tvůrce aplikací

Uživatelé v **Tvůrce aplikací** role můžou provádět vše v aplikaci Azure IoT Central, s výjimkou spravovat aplikace.

### <a name="application-operator"></a>Aplikace – operátor

Uživatelé v **operátor aplikací** roli nemají přístup k **Tvůrce aplikací** stránky. Nelze spravovat aplikace.

## <a name="manage-users"></a>Správa uživatelů

Správci aplikace můžete přiřadit uživatele k rolím v aplikaci.

### <a name="add-users"></a>Přidání uživatelů

Než mohou přihlásit a získat přístup k Azure IoT Central aplikaci, každý uživatel musí mít uživatelský účet. Accounts Microsoft (MSA) a účtů služby Azure Active Directory (Azure AD) jsou podporované v Azure IoT Central. Skupiny Azure Active Directory nejsou aktuálně podporované v Azure IoT Central.

Další informace najdete v tématu [nápovědy účtu společnosti Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) a [rychlý start: přidání nových uživatelů do služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. K přidání uživatelského účtu k aplikaci Azure IoT Central, přejděte na pomocí sekundární navigační nabídce **uživatelé** stránku **správu** části.

    ![Seznam uživatelů](media\howto-administer\image1.png)

1. Pokud chcete přidat uživatele, na **uživatelé** zvolte **+ přidat uživatele**.

    ![Přidání uživatele](media\howto-administer\image2.png)

1. Vyberte roli pro uživatele **Role** rozevírací nabídky. Další informace o rolích v *role v Azure IoT Central* části tohoto článku.

    ![Výběr role](media\howto-administer\image3.png)

    > [!NOTE]
    >  Hromadné přidání uživatelů, zadejte ID všech uživatelů, které chcete přidat uživatele oddělených středníky. Zvolte roli z **Role** rozevírací nabídky. Potom vyberte **Uložit**.

1. Poté, co přidáte uživatele, zobrazí se položka pro tohoto uživatele na **uživatelé** stránky.

    ![Seznam uživatelů](media\howto-administer\image4.png)

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Upravit role, které jsou přiřazené uživatelům

Role nelze změnit, jakmile budou přiřazena. Chcete-li změnit roli, která je přiřazena uživateli, odstraňte uživatele a pak přidejte uživatele znovu s jinou rolí.

### <a name="delete-users"></a>Odstranit uživatele

Pokud chcete odstranit uživatele, vyberte jeden nebo více políček na **uživatelé** stránky. Vyberte **Odstranit**.

## <a name="view-your-bill"></a>Prohlédněte si fakturu

Chcete-li zobrazit váš účet, přejděte na **fakturace** stránku **správy** části. Potom vyberte **fakturace**. Azure fakturační stránka se otevře na nové kartě, ve kterém uvidíte na faktuře pro jednotlivé aplikace Azure IoT Central.

## <a name="convert-your-trial-to-a-paid-application"></a>Převést na placené aplikace platnost svojí zkušební verze

Poté, co jste vyhodnotili IoT Central, můžete převést platnost svojí zkušební verze na placené aplikace. K dokončení tohoto procesu samoobslužné služby, postupujte podle těchto kroků:

1. Můžete přejít na sekundární navigační nabídce **fakturace** stránku **správu** oddílu. Pokud jste nerozšířili platnost svojí zkušební verze, bude stránka vypadat jako na následujícím snímku obrazovky:

    ![Bezplatná zkušební verze stavu](media/howto-administer/freetrial.png)

2. Vyberte **převést na placené**. Pokud jste nerozšířili platnost svojí zkušební verze, v automaticky otevíraném okně vypadá jako na následujícím snímku obrazovky:

    ![Prodlužte platnost bezplatné zkušební verze](media/howto-administer/extend.png)

3. V místním okně vyberte odpovídající tenanta Azure Active Directory a pak předplatné Azure pro vaše aplikace IoT Central.

3. Po výběru **převést**, vaše zkušební verze převede na placené aplikace a spustit načítání účtuje.

## <a name="extend-your-free-trial"></a>Prodloužit platnost bezplatné zkušební verze

Ve výchozím nastavení všechny bezplatné zkušební verze jsou k dispozici po dobu sedmi dní. Pokud chcete zvýšit vaše zkušební verze na 30 dnů, postupujte podle těchto kroků:

1. Můžete přejít na sekundární navigační nabídce **fakturace** stránku **správu** oddílu.

1. Vyberte **prodloužit platnost zkušební verze**. V místním okně vyberte odpovídající tenanta Azure Active Directory a potom předplatné Azure pro vaše aplikace IoT Central.

1. Potom vyberte **rozšířit**. Platnost svojí zkušební verze je teď platný po dobu 30 dnů.

## <a name="use-the-azure-sdks-for-control-plane-operations"></a>Použití sady Azure SDK pro operace roviny řízení

IoT Central Azure Resource Manageru SDK balíčky jsou k dispozici pro uzel, Python, C#, Ruby, Java a Go. Tyto knihovny podpory operace roviny řízení pro IoT Central, které vám umožní vytvořit, zobrazit, aktualizovat nebo odstranit aplikace IoT Central. Obsahují taky pomocné rutiny pro práci s ověřováním a Chyba zpracování, která je specifická pro jednotlivé jazyky. 

Můžete najít příklady použití sady SDK Azure Resource Manageru v [ https://github.com/emgarten/iotcentral-arm-sdk-examples ](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Další informace, podívejte se na tyto balíčky na Githubu.

| Jazyk | Úložiště | Balíček |
| ---------| ---------- | ------- |
| Node | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Přejít | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak spravovat aplikace Azure IoT Central, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Nastavení šablony zařízení](howto-set-up-template.md)
