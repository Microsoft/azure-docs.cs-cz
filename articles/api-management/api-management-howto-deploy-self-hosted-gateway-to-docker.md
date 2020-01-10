---
title: Nasazení brány Azure API Management pro místní hostování do Docker | Microsoft Docs
description: Postup nasazení brány Azure API Management v místním prostředí do Docker
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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768499"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Nasazení samoobslužné brány Azure API Management do Docker

Tento článek popisuje kroky pro nasazení místní hostované brány Azure API Management do prostředí Docker.

> [!NOTE]
> Funkce brány pro samoobslužné hostování je ve verzi Preview. V rámci verze Preview je brána v místním prostředí dostupná jenom pro vývojáře a úrovně Premium bez dalších poplatků. Úroveň pro vývojáře je omezená na jediné nasazení samoobslužné brány.

## <a name="prerequisites"></a>Požadavky

- Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)
- Vytvořte prostředí Docker. [Docker pro stolní počítače](https://www.docker.com/products/docker-desktop) je vhodnou možností pro účely vývoje a vyhodnocení. Informace o všech edicích Docker, jejich funkcích a komplexní dokumentaci k samotnému Docker najdete v [dokumentaci k Docker](https://docs.docker.com) .
- [Zřízení prostředku brány v instanci API Management](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Samostatná hostská brána je zabalená jako kontejner Docker založený na platformě X86-64.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Nasazení samoobslužné brány do Docker

1. V části **Nastavení**vyberte **brány** .
2. Vyberte prostředek brány, který hodláte nasadit.
3. Vyberte **nasazení**.
4. Všimněte si, že nový token v textovém poli **token** byl automaticky vygenerován za použití výchozích hodnot **vypršení platnosti** a **tajného klíče** . V případě potřeby upravte buď nebo, a pokud je to potřeba **, vyberte vytvořit a vytvořte** nový token.
4. Ujistěte se, že je v části **skripty nasazení**vybraná možnost **Docker** .
5. Vyberte odkaz na soubor **env. conf** vedle **prostředí** a Stáhněte si soubor.
6. Vyberte možnost **Kopírovat** ikonu umístěnou na pravé straně textového pole **Spustit** k uložení příkazu Docker do schránky.
7. Vložte příkaz do okna terminálu (nebo příkazu). Podle potřeby upravte mapování portů a název kontejneru. Všimněte si, že příkaz očekává, že se stažený soubor prostředí nachází v aktuálním adresáři.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Spusťte příkaz. Příkaz dá prostředí Docker pokyn ke spuštění kontejneru pomocí Image brány v místním prostředí staženého z Microsoft Container Registry a k mapování portů HTTP (8080) a HTTPS (8081) na porty 80 a 443 na hostiteli.
9. Spuštěním následujícího příkazu ověřte, že brána pod je spuštěná:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Vraťte se na Azure Portal a ověřte, že uzel Brána, který jste právě nasadili, hlásí stav v pořádku.

![stav brány](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> Pomocí příkazu <code>console docker container logs <gateway-name></code> můžete zobrazit snímek protokolu brány v místním prostředí.
>
> Pomocí příkazu <code>docker container logs --help</code> zobrazíte všechny možnosti zobrazení protokolu.

## <a name="next-steps"></a>Další kroky

* Další informace o samoobslužné bráně najdete v tématu [Přehled služby Azure API Management v místním prostředí pro samoobslužné hostování](self-hosted-gateway-overview.md).
* [Nakonfigurujte vlastní název domény pro samoobslužnou bránu](api-management-howto-configure-custom-domain-gateway.md).
