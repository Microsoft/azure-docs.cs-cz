---
title: Správa aplikace Azure IoT Central | Dokumentace Microsoftu
description: Jako správce jak spravovat aplikace Azure IoT Central
author: viv-liu
ms.author: viviali
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: b43e496323da75dfc63f889283257fac41f48abf
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56117727"
---
# <a name="administer-your-iot-central-application"></a>Spravovat aplikace IoT Central

Po vytvoření aplikace IoT Central, můžete přejít na **správu** části:

- Spravovat nastavení aplikace
- Správa uživatelů
- Správa rolí
- Prohlédněte si fakturu
- Přejděte k průběžným platbám platnost svojí zkušební verze
- Exportovat data
- Spravovat připojení zařízení
- Použití přístupových tokenů

Pro přístup k a použít **správu** oddíl, musí být v **správce** role pro aplikaci Azure IoT Central. Pokud vytvoříte aplikaci Azure IoT Central, je automaticky přiřazen k **správce** role pro danou aplikaci. [Spravovat uživatele](#manage-users) části v tomto článku obsahuje další informace o tom, jak přiřadit **správce** role jiným uživatelům.

## <a name="manage-application-settings"></a>Spravovat nastavení aplikace

### <a name="change-application-name-and-url"></a>Změna názvu aplikace a adresu URL
V **nastavení aplikace** stránky, můžete změnit název a adresu URL vaší aplikace a pak vyberte **Uložit**.

![Stránka nastavení aplikace](media/howto-administer-experimental/image0-a.png)

> [!Note]
> Pokud změníte adresu URL, můžete původní adresu URL provedenou jiný zákazník Azure IoT Central. Pokud k tomu dojde, není již k dispozici pro použití. Když změníte adresu URL, původní adresa URL již není funkční a budete muset informovat uživatele o nové adrese URL použít.

### <a name="prepare-and-upload-image"></a>Příprava a nahrávání image
Chcete-li změnit image aplikace, [Prepare a nahrávání imagí do aplikace Azure IoT Central](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

### <a name="copy-an-application"></a>Kopírovat aplikaci
Můžete vytvořit kopii jakékoli aplikace, bez jakékoli instance zařízení, data historie zařízení a uživatelská data. Kopie bude aplikace s průběžnými platbami, která vám bude účtovat. Tímto způsobem nelze vytvořit zkušební verzi aplikace.

Klikněte na tlačítko **kopírování** tlačítko. V dialogovém okně zadejte podrobnosti pro novou aplikaci s průběžnými platbami. Klikněte **kopírování** potvrďte, že chcete pokračovat. Další informace o polích v tomto formuláři v [vytvořit aplikaci](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) rychlý start.

![Stránka nastavení aplikace](media/howto-administer-experimental/appCopy2.png)

Po úspěšném provedení operace kopie aplikace, můžete přejít na novou aplikaci, která byla vytvořena tak, že zkopírujete svoji aplikaci pomocí odkazu, který se zobrazí.

![Stránka nastavení aplikace](media/howto-administer-experimental/appCopy3.png)

> [!Note]
> Kopírování aplikace také zkopíruje definice pravidla a akce. Ale vzhledem k tomu, že uživatelé, kteří mají přístup k původní aplikace nejsou zkopírovány do zkopírovaný aplikace, budete muset ručně přidat uživatele do akce, jako je e-mailu, pro které uživatelé jsou požadovány. Obecně je vhodné zkontrolovat pravidla a akce, abyste měli jistotu, že jsou aktuální v nové aplikaci.

### <a name="delete-an-application"></a>Odstranění aplikace

> [!Note]
> Aplikaci odstranit, musíte také mít oprávnění k odstranění prostředků v předplatném Azure jste zvolili při vytváření aplikace. Další informace najdete v tématu [použití řízení přístupu na základě rolí ke správě přístupu k prostředkům předplatného Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

Použití **odstranit** tlačítko pro trvalé odstranění aplikace IoT Central. Tím se trvale odstraní všechna data, která má přidruženou k této aplikaci.

## <a name="manage-users"></a>Správa uživatelů

### <a name="add-users"></a>Přidání uživatelů

Než mohou přihlásit a získat přístup k Azure IoT Central aplikaci, každý uživatel musí mít uživatelský účet. Accounts Microsoft (MSA) a účtů služby Azure Active Directory (Azure AD) jsou podporované v Azure IoT Central. Skupiny Azure Active Directory nejsou aktuálně podporované v Azure IoT Central.

Další informace najdete v tématu [nápovědy účtu společnosti Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) a [rychlý start: Přidání nových uživatelů do služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Chcete-li přidat uživatele k IoT Central aplikaci, přejděte na **uživatelé** stránku **správu** oddílu.

    ![Seznam uživatelů](media/howto-administer-experimental/image1.png)

1. Pokud chcete přidat uživatele, na **uživatelé** zvolte **+ přidat uživatele**.

1. Vyberte roli pro uživatele **Role** rozevírací nabídky. Další informace o rolích v [spravovat role](#manage-roles) části tohoto článku.

    ![Výběr role](media/howto-administer-experimental/image3.png)

    > [!NOTE]
    >  Hromadné přidání uživatelů, zadejte ID všech uživatelů, které chcete přidat uživatele oddělených středníky. Zvolte roli z **Role** rozevírací nabídky. Potom vyberte **Uložit**.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Upravit role, které jsou přiřazené uživatelům

Role nelze změnit, jakmile budou přiřazena. Chcete-li změnit roli, která je přiřazena uživateli, odstraňte uživatele a pak přidejte uživatele znovu s jinou rolí.

### <a name="delete-users"></a>Odstranit uživatele

Pokud chcete odstranit uživatele, vyberte jeden nebo více políček na **uživatelé** stránky. Vyberte **Odstranit**.

## <a name="manage-roles"></a>Správa rolí

Role umožňují řídit, kdo ve vaší organizaci můžete provádět různé úlohy v IoT Central. Existují tři role, které můžete přiřazovat uživatelům vaší aplikace. 

### <a name="administrator"></a>Správce

Uživatelé v **správce** role mají přístup ke všem funkcím v aplikaci.

Uživatel, který vytváří aplikace se automaticky přiřadí **správce** role. Musí být vždy alespoň jeden uživatel ve **správce** role.

### <a name="application-builder"></a>Tvůrce aplikací

Uživatelé v **Tvůrce aplikací** role můžou provádět vše v aplikaci s tím rozdílem, správě aplikace. To znamená, že tvůrci můžete vytvořit, upravit, odstranit šablon zařízení a zařízení, Správa sad zařízení a spouštět úlohy a analýzy. Tvůrci nebudete mít přístup k **správu** části aplikace.

### <a name="application-operator"></a>Operátor aplikace

Uživatelé v **operátor aplikací** role nemůže provádět změny šablon a nelze spravovat aplikace. To znamená, že operátory lze přidání a odstranění zařízení, Správa sad zařízení a spouštět úlohy a analýzy. Operátory nebudete mít přístup k **Tvůrce aplikací** a **správu** stránky.


## <a name="view-your-bill"></a>Prohlédněte si fakturu

Chcete-li zobrazit váš účet, přejděte na **fakturace** stránku **správy** části. Azure fakturační stránka se otevře na nové kartě, ve kterém uvidíte na faktuře pro jednotlivé aplikace Azure IoT Central.

### <a name="convert-your-trial-to-pay-as-you-go"></a>Přejděte k průběžným platbám platnost svojí zkušební verze

Můžete převést zkušební verzi aplikace do aplikace s průběžnými platbami. Zde jsou rozdíly mezi těmito typy aplikací.

- **Trial** – Zkušební verze aplikací jsou bezplatné a jejich platnost vyprší po 7 dnech. Před vypršením platnosti je možné je kdykoli převést na průběžné platby.
- **Pay-As-You-Go** – Aplikace s průběžnými platbami se účtují podle počtu zařízení a prvních 5 zařízení je zdarma.

Další informace o cenách najdete na [stránce s cenami za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).
    
K dokončení tohoto procesu samoobslužné služby, postupujte podle těchto kroků:

1. Přejděte na **fakturace** stránku **správu** oddílu. 

    ![Zkušební verze stavu](media/howto-administer-experimental/freetrialbilling.png)

1. Klikněte na tlačítko **přejděte k průběžným platbám**. 

    ![Převést zkušební verzi](media/howto-administer-experimental/convert.png)

1. Vyberte odpovídající Azure Active Directory a pak předplatné Azure, které chcete použít pro vaši aplikaci s průběžnými platbami.

1. Po kliknutí na **převést**, vaše aplikace je teď aplikace s průběžnými platbami a spustit načítání účtuje.

## <a name="export-data"></a>Exportovat data

Můžete povolit **nepřetržitý export dat** exportovat měření, zařízení a zařízení šablony dat do účtu úložiště objektů Blob v Azure. Další informace o tom, jak [exportovat data](#export-data).

## <a name="manage-device-connection"></a>Spravovat připojení zařízení

Připojení zařízení v měřítku ve vaší aplikaci pomocí klíčů a certifikátů tady. Další informace o [připojení zařízení](/azure/iot-central/concepts-connectivity-experimental?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="use-access-tokens"></a>Použití přístupových tokenů

Generovat přístupové tokeny pro jejich použití v nástrojích pro vývojáře. Aktuálně je jeden vývojářský nástroj k dispozici tedy IoT Central explorer pro zprávy typu zařízení a změny v propreties a nastavení monitorování. Další informace o Průzkumníku IoT Central. 

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
> [Nastavení šablony zařízení](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
