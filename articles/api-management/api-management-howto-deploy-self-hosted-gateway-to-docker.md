---
title: Nasazení brány azure api s vlastním hostitelem do Dockeru | Dokumenty společnosti Microsoft
description: Přečtěte si, jak do Dockeru nasadit bránu pro správu Azure API s vlastním hostitelem
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: b5acda30f7f4a01e1b7b6ac82652452c3c40e3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768499"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Nasazení brány s vlastním hostitelem azure api managementu do Dockeru

Tento článek obsahuje postup nasazení brány azure api managementu s vlastním hostitelem do prostředí Dockeru.

> [!NOTE]
> Funkce brány s vlastním hostitelem je ve verzi Preview. Během předběžné verze je brána s vlastním hostitelem dostupná pouze na úrovních Vývojář a Premium bez dalších poplatků. Úroveň pro vývojáře je omezena na jedno nasazení brány hostované sami sebou.

## <a name="prerequisites"></a>Požadavky

- Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)
- Vytvořte prostředí Dockeru. [Docker pro desktop](https://www.docker.com/products/docker-desktop) je dobrou volbou pro účely vývoje a hodnocení. Informace o všech edicích Dockeru, jejich funkcích a komplexní dokumentaci o samotném Dockeru najdete v [dokumentaci](https://docs.docker.com) k Dockeru.
- [Zřízení prostředku brány v instanci správy rozhraní API](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Brána s vlastním hostitelem je zabalena jako kontejner Dockeru založený na linuxovém systému x86-64.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Nasazení brány s vlastním hostitelem do Dockeru

1. V části **Nastavení**vyberte **Brány.**
2. Vyberte prostředek brány, který chcete nasadit.
3. Vyberte **možnost Nasazení**.
4. Všimněte si, že nový token v textovém poli **tokenu** byl automaticky vygenerován pro vás pomocí výchozí **hodnoty vypršení platnosti** a **tajný klíč.** V případě potřeby upravte jeden nebo oba a vyberte **Generovat** a vytvořte nový token.
4. Ujistěte se, že je v části **Skripty nasazení**vybrán **o** dockeru .
5. Chcete-li soubor stáhnout, vyberte odkaz na soubor **env.conf** vedle **prostředí.**
6. Vyberte ikonu **kopírování** umístěnou na pravém konci textového pole **Spustit,** chcete-li příkaz Docker uložit do schránky.
7. Vložte příkaz do okna terminálu (nebo příkazu). Podle potřeby upravte mapování portů a název kontejneru. Všimněte si, že příkaz očekává, že stažený soubor prostředí bude přítomen v aktuálním adresáři.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Spusťte příkaz. Příkaz instruuje vaše prostředí Dockeru ke spuštění kontejneru pomocí bitové kopie brány s vlastním hostitelem stažené z registru kontejnerů Microsoft a k mapování portů HTTP (8080) a HTTPS (8081) kontejneru na porty 80 a 443 na hostiteli.
9. Spuštěním níže uvedeného příkazu zkontrolujte, zda je pod brány spuštěný:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Vraťte se na portál Azure a potvrďte, že uzel brány, který jste právě nasadili, hlásí stav v pořádku.

![stav brány](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> Pomocí <code>console docker container logs <gateway-name></code> příkazu můžete zobrazit snímek samoobslužného protokolu brány.
>
> Pomocí <code>docker container logs --help</code> příkazu zobrazíte všechny možnosti zobrazení protokolu.

## <a name="next-steps"></a>Další kroky

* Další informace o samoobslužné bráně najdete v [tématu Přehled samoobslužné brány správy rozhraní Azure API](self-hosted-gateway-overview.md).
* [Konfigurace vlastního názvu domény pro samoobslužnou bránu](api-management-howto-configure-custom-domain-gateway.md).
