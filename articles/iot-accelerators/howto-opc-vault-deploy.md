---
title: Jak nasadit službu správy certifikátů OPC Vault – Azure | Dokumenty společnosti Microsoft
description: Jak nasadit službu správy certifikátů OPC Vault od začátku.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f577059e1ebf70e3a9dfe9e538a9d3d49d7c8e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71199992"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Vytvoření a nasazení služby správy certifikátů OPC Vault

Tento článek vysvětluje, jak nasadit službu správy certifikátů OPC Vault v Azure.

> [!NOTE]
> Další informace naleznete v [úložišti](https://github.com/Azure/azure-iiot-opc-vault-service)GitHub OPC Vault .

## <a name="prerequisites"></a>Požadavky

### <a name="install-required-software"></a>Instalace požadovaného softwaru

V současné době sestavení a nasazení operace je omezena na systém Windows.
Ukázky jsou všechny napsané pro C# .NET Standard, které je třeba vytvořit službu a ukázky pro nasazení.
Všechny nástroje, které potřebujete pro rozhraní .NET Standard, jsou dodávány s nástroji .NET Core. Viz [Začínáme s rozhraním .NET Core](https://docs.microsoft.com/dotnet/articles/core/getting-started).

1. [Nainstalujte rozhraní .NET Core 2.1+][dotnet-install].
2. [Nainstalujte Docker][docker-url] (volitelné, pouze v případě, že je vyžadováno místní sestavení Dockeru).
4. Nainstalujte [nástroje příkazového řádku Azure pro PowerShell][powershell-install].
5. Zaregistrujte si [předplatné Azure][azure-free].

### <a name="clone-the-repository"></a>Klonování úložiště

Pokud jste tak ještě neučinili, klonovat toto úložiště GitHub. Otevřete příkazový řádek nebo terminál a spusťte následující:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

Případně můžete klonovat repo přímo v sadě Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Vytváření a nasazování služby Azure ve Windows

Skript prostředí PowerShell poskytuje snadný způsob nasazení mikroslužby OPC Vault a aplikace.

1. Otevřete okno prostředí PowerShell v kořenovém adresáři pro opětovné poto. 
3. Přejděte do `cd deploy`složky nasazení .
3. Zvolte název, který `myResourceGroup` pravděpodobně nezpůsobí konflikt s jinými nasazenými webovými stránkami. Viz část "Název webové stránky, který je již používán" dále v tomto článku.
5. Spusťte `.\deploy.ps1` nasazení s pro interaktivní instalaci nebo zadejte úplný příkazový řádek:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Pokud máte v plánu vyvíjet `-development 1` s tímto nasazením, přidejte povolit swagger ui a nasadit sestavení ladění.
6. Podle pokynů ve skriptu se přihlaste k předplatnému a zadejte další informace.
9. Po úspěšné operaci sestavení a nasazení byste měli zobrazit následující zprávu:
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
   > V případě problémů naleznete v části "Řešení potíží s chybami nasazení" dále v článku.

8. Otevřete svůj oblíbený prohlížeč a otevřete stránku aplikace:`https://myResourceGroup.azurewebsites.net`
8. Podejte webové aplikaci a mikroslužbě OPC Vault několik minut, aby se po nasazení zahřály. Webová domovská stránka může viset při prvním použití, po dobu až jedné minuty, dokud nezískáte první odpovědi.
11. Chcete-li se podívat na rozhraní Api Swagger, otevřete:`https://myResourceGroup-service.azurewebsites.net`
13. Chcete-li spustit místní server GDS `.\myResourceGroup-gds.cmd`s dotnet, spusťte program . S Docker, `.\myResourceGroup-dockergds.cmd`start .

Je možné znovu nasadit sestavení s přesně stejným nastavením. Uvědomte si, že taková operace obnovuje všechny tajné klíče aplikace a může obnovit některá nastavení v registraci aplikací Služby Azure Active Directory (Azure AD).

Je také možné znovu nasadit pouze binární soubory webové aplikace. S parametrem `-onlyBuild 1`, nové zip balíčky služby a aplikace jsou nasazeny do webových aplikací.

Po úspěšném nasazení můžete začít používat služby. Viz [Správa služby správy certifikátů OPC Vault](howto-opc-vault-manage.md).

## <a name="delete-the-services-from-the-subscription"></a>Odstranění služeb z předplatného

Zde je uveden postup:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Přejděte do skupiny prostředků, ve které byla služba nasazena.
3. Vyberte **Odstranit skupinu prostředků** a potvrďte tuto akci.
4. Po krátké době jsou odstraněny všechny součásti nasazených služeb.
5. Přejděte na**registrace aplikací** **Azure Active Directory** > .
6. Pro každou nasazenou skupinu prostředků by měly být uvedeny tři registrace. Registrace mají následující `resourcegroup-client`názvy: `resourcegroup-module` `resourcegroup-service`, , . Každou registraci odstraňte samostatně.

Nyní jsou odebrány všechny nasazené součásti.

## <a name="troubleshooting-deployment-failures"></a>Poradce při potížích s chybami nasazení

### <a name="resource-group-name"></a>Název skupiny prostředků

Použijte krátký a jednoduchý název skupiny prostředků. Název se také používá k pojmenování prostředků a předpony adresy URL služby. Jako takový musí splňovat požadavky na pojmenování prostředků.  

### <a name="website-name-already-in-use"></a>Název webu, který je již používán

Je možné, že název webu je již používán. Je třeba použít jiný název skupiny prostředků. Názvy hostitelů, které skript https://resourcegroupname.azurewebsites.net nasazení https://resourgroupname-service.azurewebsites.netpoužívá, jsou: a .
Jiné názvy služeb jsou vytvořeny kombinací zapisovatelné hořkosti krátkých názvů a je nepravděpodobné, že by byly v konfliktu s jinými službami.

### <a name="azure-ad-registration"></a>Registrace v Azure AD 

Skript nasazení se pokusí zaregistrovat tři aplikace Azure AD ve službě Azure AD. V závislosti na vašich oprávněních ve vybraném tenantovi Azure AD může tato operace selhat. Existují dvě možnosti:

- Pokud jste zvolili klienta Azure AD ze seznamu klientů, restartujte skript a zvolte jiný ze seznamu.
- Případně nasadit privátní ho klienta Azure AD v jiném předplatném. Restartujte skript a vyberte jeho použití.

## <a name="deployment-script-options"></a>Možnosti skriptu nasazení

Skript má následující parametry:


```
-resourceGroupName
```

Může se jedná o název existující nebo nové skupiny prostředků.

```
-subscriptionId
```


Toto je ID předplatného, kde se nasadí prostředky. Zpráva není povinná.

```
-subscriptionName
```


Případně můžete použít název předplatného.

```
-resourceGroupLocation
```


Toto je umístění skupiny prostředků. Pokud je zadán, pokusí se tento parametr vytvořit novou skupinu prostředků v tomto umístění. Tento parametr je také volitelný.


```
-tenantId
```


Toto je klient azure ad použít. 

```
-development 0|1
```

Toto je nasazení pro vývoj. Použijte sestavení ladění a nastavte ASP.NET prostředí na vývoj. Vytvořte `.publishsettings` pro import ve Visual Studiu 2017, aby mohl přímo nasadit aplikaci a službu. Tento parametr je také volitelný.

```
-onlyBuild 0|1
```

Toje znovu sestavit a znovu nasadit pouze webové aplikace a znovu kontejnery Dockeru. Tento parametr je také volitelný.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Další kroky

Nyní, když jste se naučili nasadit OPC Vault od nuly, můžete:

> [!div class="nextstepaction"]
> [Správa trezoru OPC](howto-opc-vault-manage.md)
