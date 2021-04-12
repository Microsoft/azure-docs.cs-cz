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
ms.openlocfilehash: 98dc9345d2c8b392fd094458b612857d6d454739
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646056"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Sestavení a nasazení služby správy certifikátů úložiště OPC

> [!IMPORTANT]
> I když aktualizujeme Tento článek, přečtěte si nejaktuálnější obsah v tématu [Azure Data IoT](https://azure.github.io/Industrial-IoT/) .

Tento článek vysvětluje, jak nasadit službu správy certifikátů trezoru OPC v Azure.

> [!NOTE]
> Další informace najdete v [úložišti OPC trezoru](https://github.com/Azure/azure-iiot-opc-vault-service)GitHubu.

## <a name="prerequisites"></a>Požadavky

### <a name="install-required-software"></a>Nainstalovat požadovaný software

V současné době je operace sestavení a nasazení omezená na Windows.
Ukázky jsou napsané pro C# .NET Standard, které potřebujete k vytvoření služby a ukázek pro nasazení.
Všechny nástroje, které potřebujete pro .NET Standard, jsou dodávány s nástroji .NET Core. Viz Začínáme [s .NET Core](/dotnet/articles/core/getting-started).

1. [Nainstalujte .NET Core 2.1 +][dotnet-install].
2. [Nainstalovat Docker][docker-url] (volitelné, jenom pokud je vyžadováno místní sestavení Docker).
4. Nainstalujte [nástroje příkazového řádku Azure pro PowerShell][powershell-install].
5. Zaregistrujte si [předplatné Azure][azure-free].

### <a name="clone-the-repository"></a>Klonování úložiště

Pokud jste to ještě neudělali, naklonujte toto úložiště GitHub. Otevřete příkazový řádek nebo terminál a spusťte následující příkaz:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

Případně můžete klonovat úložiště přímo v aplikaci Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Sestavení a nasazení služby Azure ve Windows

PowerShellový skript nabízí snadný způsob nasazení mikroslužby trezoru OPC a aplikace.

1. Otevřete okno PowerShellu v kořenovém adresáři úložiště. 
3. Přejít do složky Deploy (nasadit) `cd deploy` .
3. Vyberte název `myResourceGroup` , který není pravděpodobně příčinou konfliktu s jinými nasazenými webové stránky. V části dále v tomto článku najdete informace o tom, že se už používá název webu.
5. Spusťte nasazení pomocí `.\deploy.ps1` příkazu pro interaktivní instalaci nebo zadejte úplný příkazový řádek:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Pokud plánujete vývoj s tímto nasazením, přidejte, `-development 1` abyste povolili uživatelské rozhraní Swagger a nasadili sestavení ladění.
6. Pokud se chcete přihlásit ke svému předplatnému a zadat další informace, postupujte podle pokynů ve skriptu.
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

   > [!NOTE]
   > V případě problémů se v části řešení potíží s nasazením v tomto článku přečtěte v části "selhání nasazení.

8. Otevřete oblíbený prohlížeč a otevřete stránku aplikace: `https://myResourceGroup.azurewebsites.net`
8. Poskytněte webové aplikaci a mikroslužbu trezoru OPC pár minut, než se zahřívá po nasazení. Domovská stránka webu může při prvním použití přestat reagovat, dokud neobdržíte první odpověď.
11. Pokud si chcete prohlédnout rozhraní Swagger API, otevřete: `https://myResourceGroup-service.azurewebsites.net`
13. Pokud chcete spustit místní GDS Server pomocí dotnet, spusťte `.\myResourceGroup-gds.cmd` . V Docker začněte `.\myResourceGroup-dockergds.cmd` .

Je možné znovu nasadit sestavení s přesně stejnými nastaveními. Počítejte s tím, že taková operace obnoví všechny tajné klíče aplikace a může resetovat některá nastavení v Azure Active Directory (Azure AD) registrace aplikací.

Je také možné znovu nasadit pouze binární soubory webové aplikace. S parametrem `-onlyBuild 1` jsou nové balíčky zip služby a aplikace nasazeny do webových aplikací.

Po úspěšném nasazení můžete začít používat služby. Viz [Správa služby správy certifikátů trezoru OPC](howto-opc-vault-manage.md).

## <a name="delete-the-services-from-the-subscription"></a>Odstranit služby z předplatného

Zde je uveden postup:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejít do skupiny prostředků, ve které byla služba nasazena.
3. Vyberte **Odstranit skupinu prostředků** a potvrďte tuto akci.
4. Po krátké době se všechny nasazené součásti služby odstraní.
5. Přejít na **Azure Active Directory**  >  **Registrace aplikací**.
6. Pro každou nasazenou skupinu prostředků by měly být uvedené tři registrace. Registrace mají následující názvy: `resourcegroup-client` , `resourcegroup-module` , `resourcegroup-service` . Každou registraci odstraňte samostatně.

Všechny nasazené součásti se teď odeberou.

## <a name="troubleshooting-deployment-failures"></a>Řešení potíží se selháním nasazení

### <a name="resource-group-name"></a>Název skupiny prostředků

Použijte krátký a jednoduchý název skupiny prostředků. Název se používá také k pojmenování prostředků a předpony adresy URL služby. V takovém případě musí vyhovovat požadavkům na pojmenování prostředků.  

### <a name="website-name-already-in-use"></a>Název webu se už používá.

Je možné, že název webu se už používá. Musíte použít jiný název skupiny prostředků. Názvy hostitelů používané skriptem nasazení jsou: https: \/ /ResourceGroupName.azurewebsites.NET a https: \/ /resourgroupname-Service.azurewebsites.NET.
Jiné názvy služeb jsou sestaveny kombinací krátkých hodnot hash názvů a pravděpodobně nejsou v konfliktu s jinými službami.

### <a name="azure-ad-registration"></a>Registrace v Azure AD 

Skript nasazení se pokusí zaregistrovat tři aplikace Azure AD ve službě Azure AD. Tato operace může selhat v závislosti na vašich oprávněních ve vybraném tenantovi služby Azure AD. Existují dvě možnosti:

- Pokud jste zvolili tenanta Azure AD ze seznamu tenantů, restartujte skript a zvolte jiný ze seznamu.
- Případně můžete nasadit privátního tenanta Azure AD v jiném předplatném. Restartujte skript a vyberte ho pro použití.

## <a name="deployment-script-options"></a>Možnosti skriptu nasazení

Skript má následující parametry:


```
-resourceGroupName
```

Může to být název existující nebo nové skupiny prostředků.

```
-subscriptionId
```


Toto je ID předplatného, kde budou nasazeny prostředky. Zpráva není povinná.

```
-subscriptionName
```


Případně můžete použít název předplatného.

```
-resourceGroupLocation
```


Toto je umístění skupiny prostředků. Když se tento parametr zadá, v tomto umístění se pokusí vytvořit novou skupinu prostředků. Tento parametr je také volitelný.


```
-tenantId
```


Toto je tenant služby Azure AD, který se má použít. 

```
-development 0|1
```

Toto je nasazení pro vývoj. Použijte ladění buildu a nastavte prostředí ASP.NET na vývoj. Vytvoření `.publishsettings` pro import v aplikaci Visual Studio 2017, aby bylo možné aplikaci a službu nasadit přímo. Tento parametr je také volitelný.

```
-onlyBuild 0|1
```

Jedná se o opětovné sestavení a opětovné nasazení pouze webových aplikací a k opětovnému sestavení kontejnerů Docker. Tento parametr je také volitelný.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nasadit trezor OPC od začátku, můžete:

> [!div class="nextstepaction"]
> [Správa trezoru OPC](howto-opc-vault-manage.md)