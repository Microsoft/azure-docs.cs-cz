---
title: Nasazení samoobslužné brány do Docker | Microsoft Docs
description: Přečtěte si, jak nasadit komponentu samoobslužné brány Azure API Management do Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: b9e990988770e8aca015ae8b1159bb4f5e50df57
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "82205089"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Nasazení samoobslužné brány Azure API Management do Docker

Tento článek popisuje postup nasazení komponenty samoobslužné brány Azure API Management do prostředí Docker.

> [!NOTE]
> Hostování samoobslužné brány v Docker se nejlépe hodí pro případy použití při vyhodnocování a vývoji. Kubernetes se doporučuje pro produkční použití. V [tomto](how-to-deploy-self-hosted-gateway-kubernetes.md) dokumentu se dozvíte, jak nasadit samoobslužnou bránu do Kubernetes.

## <a name="prerequisites"></a>Předpoklady

- Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)
- Vytvořte prostředí Docker. [Docker pro stolní počítače](https://www.docker.com/products/docker-desktop) je vhodnou možností pro účely vývoje a vyhodnocení. Informace o všech edicích Docker, jejich funkcích a komplexní dokumentaci k samotnému Docker najdete v [dokumentaci k Docker](https://docs.docker.com) .
- [Zřízení prostředku brány v instanci API Management](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Samostatná hostská brána je zabalená jako kontejner Docker založený na platformě X86-64.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Nasazení samoobslužné brány do Docker

1. V části **nasazení a infrastruktura** vyberte **brány** .
2. Vyberte prostředek brány, který hodláte nasadit.
3. Vyberte **nasazení**.
4. Všimněte si, že přístupový token v textovém poli **token** byl automaticky vygenerován za použití výchozích hodnot **vypršení platnosti** a **tajného klíče** . V případě potřeby vyberte požadované hodnoty v jednom nebo obou ovládacích prvcích pro vygenerování nového tokenu.
4. Ujistěte se, že je v části **skripty nasazení** vybraná možnost **Docker** .
5. Vyberte odkaz na soubor **env. conf** vedle **prostředí** a Stáhněte si soubor.
6. Vyberte možnost **Kopírovat** ikonu umístěnou na pravé straně textového pole **Spustit** pro zkopírování příkazu Docker do schránky.
7. Vložte příkaz do okna terminálu (nebo příkazu). Podle potřeby upravte mapování portů a název kontejneru. Všimněte si, že příkaz předpokládá, že stažený soubor prostředí je přítomen v aktuálním adresáři.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Spusťte příkaz. Příkaz instruuje prostředí Docker za účelem spuštění kontejneru pomocí [Image kontejneru](https://aka.ms/apim/sputnik/dhub) stažené z Microsoft Container registry a mapování portů HTTP (8080) a HTTPS (8081) na porty 80 a 443 na hostiteli.
9. Spusťte následující příkaz, který zkontroluje, jestli je kontejner brány spuštěný:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Vraťte se na Azure Portal, klikněte na **Přehled** a potvrďte, že kontejner samoobslužné brány, který jste právě nasadili, hlásí dobrý stav.

![stav brány](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> Pomocí <code>console docker container logs <gateway-name></code> příkazu můžete zobrazit snímek protokolu brány pro samoobslužné hostování.
>
> Pomocí <code>docker container logs --help</code> příkazu zobrazíte všechny možnosti zobrazení protokolu.

## <a name="next-steps"></a>Další kroky

* Další informace o samoobslužné bráně najdete v tématu [Přehled služby Azure API Management v místním prostředí pro samoobslužné hostování](self-hosted-gateway-overview.md).
* [Nakonfigurujte vlastní název domény pro samoobslužnou bránu](api-management-howto-configure-custom-domain-gateway.md).
