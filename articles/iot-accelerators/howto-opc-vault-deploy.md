---
title: Jak nasadit službu správy certifikátů trezoru OPC – Azure | Microsoft Docs
description: Jak nasadit službu správy certifikátů trezoru OPC od začátku.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 347e7c2aa2ff4fb4f188847b81d03006c1909166
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997653"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Sestavení a nasazení služby správy certifikátů úložiště OPC

Tento článek vysvětluje, jak nasadit službu správy certifikátů trezoru OPC v Azure.

> [!NOTE]
> Další informace o podrobnostech a pokynech k nasazení najdete v [úložišti OPC trezoru](https://github.com/Azure/azure-iiot-opc-vault-service)GitHubu.

## <a name="prerequisites"></a>Požadavky

### <a name="install-required-software"></a>Nainstalovat požadovaný software

V současné době je operace sestavení a nasazení omezená na Windows.
Ukázky jsou napsané pro C# .NET Standard, který je potřeba k sestavení služby a ukázek pro nasazení.
Všechny nástroje, které potřebujete pro .NET Standard, se dodávají s nástroji .Net Core. To, co potřebujete, najdete [tady](https://docs.microsoft.com/dotnet/articles/core/getting-started) .

1. [Nainstalujte .NET Core 2.1 +][dotnet-install].
2. [Nainstalovat Docker][docker-url] (volitelné, pouze pokud je požadováno místní sestavení Docker).
4. Nainstalujte [nástroje příkazového řádku Azure pro PowerShell][powershell-install].
5. Zaregistrujte si [předplatné Azure][azure-free].

### <a name="clone-the-repository"></a>Klonování úložiště

Pokud jste to ještě neudělali, naklonujte toto úložiště GitHub.  Otevřete příkazový řádek nebo terminál a spusťte příkaz:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

nebo naklonujte úložiště přímo v aplikaci Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Sestavení a nasazení služby Azure ve Windows

Powershellový skript nabízí snadný způsob nasazení mikroslužby trezoru OPC a aplikace.<br>

1. Otevřete okno PowerShellu v kořenovém adresáři úložiště. 
3. Přejít do složky Deploy`cd deploy`
3. Vyberte název `myResourceGroup` , který není pravděpodobně příčinou konfliktu s jinými nasazenými webové stránky. [Níže](#website-name-already-in-use) naleznete informace o tom, jak se volí názvy webových stránek na základě názvu skupiny prostředků.
5. Spustit nasazení v aplikaci `.\deploy.ps1` pro interaktivní instalaci<br>
nebo zadejte úplný příkazový řádek:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Pokud plánujete vývoj s tímto nasazením, přidejte `-development 1` , abyste povolili uživatelské rozhraní Swagger a nasadili sestavení ladění.
6. Pokud se chcete přihlásit ke svému předplatnému a poskytnout další informace, postupujte podle pokynů ve skriptu.
9. Po úspěšné operaci sestavení a nasazení by se měla zobrazit následující zpráva:
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

V případě, že spustíte problémy, postupujte podle [](#troubleshooting-deployment-failures)následujících kroků.

8. Otevřete oblíbený prohlížeč a otevřete stránku aplikace:`https://myResourceGroup.azurewebsites.net`
8. Poskytněte webové aplikaci a mikroslužbu trezoru OPC pár minut, než se zahřívá po nasazení. Domovská stránka webu se může na začátku trvat až do minut, dokud nedostanete první odpovědi.
11. Pokud se chcete podívat na rozhraní API Swagger, otevřete:`https://myResourceGroup-service.azurewebsites.net`
13. Pokud chcete spustit místní GDS Server pomocí příkazu dotnet `.\myResourceGroup-gds.cmd` Start nebo Docker Start `.\myResourceGroup-dockergds.cmd`.

Jako poznámky je možné znovu nasadit sestavení s přesně stejnými nastaveními. Počítejte s tím, že taková operace obnoví všechny tajné klíče aplikace a může resetovat některá nastavení v Azure Active Directory (Azure AD) registrace aplikací.

Je také možné znovu nasadit pouze binární soubory webové aplikace. S parametrem `-onlyBuild 1` nové balíčky zip služby a aplikace se nasazují do webových aplikací.

Po úspěšném nasazení můžete začít používat tyto služby: [Správa služby správy certifikátů trezoru OPC](howto-opc-vault-manage.md)

## <a name="delete-the-certificate-management-service-from-the-subscription"></a>Odstranit službu správy certifikátů z předplatného

1. Přihlaste se k Azure Portal `https://portal.azure.com`:.
2. Přejít do skupiny prostředků, ve které byla služba nasazena.
3. Vyberte `Delete resource group` a potvrďte.
4. Po krátké době se všechny nasazené součásti služby odstraní.
5. Teď přejít na `Azure Active Directory/App registrations`.
6. Pro každou nasazenou skupinu prostředků by měly být uvedené tři registrace s následujícími názvy: `resourcegroup-client`, `resourcegroup-module`, `resourcegroup-service`.
Každou registraci je potřeba odstranit samostatně.
7. Všechny nasazené součásti se teď odeberou.

## <a name="troubleshooting-deployment-failures"></a>Řešení potíží se selháním nasazení

### <a name="resource-group-name"></a>Název skupiny prostředků

Ujistěte se, že používáte krátký a jednoduchý název skupiny prostředků.  Název se používá také k pojmenování prostředků a předpony adresy URL služby a jako takové, které musí splňovat požadavky na pojmenovávání prostředků.  

### <a name="website-name-already-in-use"></a>Název webu se už používá.

Je možné, že název webu se už používá.  Pokud narazíte na tuto chybu, je nutné použít jiný název skupiny prostředků. Názvy hostitelů používané skriptem nasazení jsou: https://resourcegroupname.azurewebsites.net a. https://resourgroupname-service.azurewebsites.net
Jiné názvy služeb jsou sestaveny kombinací krátkých hodnot hash názvů a pravděpodobně nejsou v konfliktu s jinými službami.

### <a name="azure-active-directory-azure-ad-registration"></a>Registrace Azure Active Directory (Azure AD) 

Skript nasazení se pokusí zaregistrovat tři aplikace Azure AD v Azure Active Directory.  
Tato operace může selhat v závislosti na vašich oprávněních ve vybraném tenantovi služby Azure AD.   Existují dvě možnosti:

1. Pokud jste zvolili tenanta Azure AD ze seznamu tenantů, restartujte skript a zvolte jiný ze seznamu.
2. Případně můžete nasadit privátního tenanta Azure AD v jiném předplatném, restartovat skript a vybrat ho pro použití.

## <a name="deployment-script-options"></a>Možnosti skriptu nasazení

Skript má následující parametry:


```
-resourceGroupName
```

Může to být název existující nebo nové skupiny prostředků.

```
-subscriptionId
```


Volitelné, ID předplatného, kde budou nasazeny prostředky.

```
-subscriptionName
```


Případně můžete také použít název předplatného.

```
-resourceGroupLocation
```


Volitelné, umístění skupiny prostředků. Když se tato akce zadá, pokusí se na tomto místě vytvořit novou skupinu prostředků.


```
-tenantId
```


Tenant Azure AD, který se má použít. 

```
-development 0|1
```

Volitelné, pro nasazení pro vývoj. Použijte ladit Build a nastavte prostředí ASP.Net na vývoj. Vytvořte. publishsettings pro import v aplikaci Visual Studio 2017, aby bylo možné aplikaci a službu nasadit přímo.

```
-onlyBuild 0|1
```

Volitelné, pro opětovné sestavení a opětovné nasazení pouze webových aplikací a pro opětovné sestavení kontejnerů Docker.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak nasadit trezor OPC od začátku, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Správa trezoru OPC](howto-opc-vault-manage.md)
