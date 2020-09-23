---
title: Řídicí panely Azure Data Studio
description: Řídicí panely Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: c2dd0ce3c3e9a0f1f699b58ed2657394ad9acbe1
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936383"
---
# <a name="azure-data-studio-dashboards"></a>Řídicí panely Azure Data Studio

[Azure Data Studio](https://aka.ms/azuredatastudio) nabízí podobné možnosti jako u Azure Portal pro zobrazení informací o prostředcích ARC Azure.  Tato zobrazení se nazývají **řídicí panely** a mají rozložení a možnosti, které se podobá tomu, co byste viděli o daném prostředku v Azure Portal, ale získáte flexibilitu v tom, že se tyto informace ve vašem prostředí zobrazí v místním prostředí v případě, že nemáte k dispozici připojení k Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connecting-to-a-data-controller"></a>Připojení k řadiči dat

### <a name="prerequisites"></a>Požadavky

- Stáhnout [Azure Data Studio](https://aka.ms/getazuredatastudio)
- Rozšíření Azure ARC je nainstalované.

### <a name="determine-the-data-controller-server-api-endpoint-url"></a>Určení adresy URL koncového bodu rozhraní API serveru datového kontroleru

Nejdřív budete muset připojit Azure Data Studio k adrese URL koncového bodu rozhraní API služby data Controller.

Chcete-li získat tento koncový bod, můžete spustit následující příkaz:

```console
kubectl get svc/controller-svc-external -n <namespace name>

#Example:
kubectl get svc/controller-svc-external -n arc
```

Zobrazí se výstup, který vypadá takto:

```console
NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                       AGE
controller-svc-external   LoadBalancer   10.0.175.137   52.154.152.24    30080:32192/TCP                               22h
```

Pokud používáte typ vyrovnávání zatížení, budete chtít zkopírovat externí IP adresu a číslo portu 300080.  Pokud používáte NodePort, budete chtít použít IP adresu vašeho serveru rozhraní Kubernetes API a číslo portu uvedené ve sloupci PORT (S).

Teď budete chtít vytvořit adresu URL pro koncový bod tak, že sloučíte tyto informace, například:

```console
https://<ip address>:<port>

Example:
https://52.;154.152.24:30080
```

Poznamenejte si IP adresu, jak ji budete používat v dalším kroku.

### <a name="connect"></a>Připojit

1. Otevřít Azure Data Studio

1. Na levé straně vyberte kartu **připojení** .

Směrem k dolnímu rohu rozbalte panel s názvem **řadiče Azure ARC**.

Kliknutím na ikonu + přidejte nové připojení k řadiči dat.

V horní části obrazovky v paletě příkazů zadejte adresu URL vytvořenou v kroku 1, klikněte na ENTER.
Zadejte uživatelské jméno pro řadič dat.  Toto bylo hodnota uživatelského jména, kterou jste předali během nasazování řadiče dat.  Klikněte na tlačítko ENTER.
Zadejte heslo pro řadič dat.  Toto bylo hodnota hesla, kterou jste předali během nasazování řadiče dat. Klikněte na tlačítko ENTER.

Teď, když jste připojeni k řadiči dat, si můžete zobrazit řídicí panely pro řadič dat a všechny spravované instance SQL nebo PostgreSQL prostředky skupiny serverů se škálováním na více instancí.

## <a name="view-the-data-controller-dashboard"></a>Zobrazení řídicího panelu řadiče dat

Pravým tlačítkem myši klikněte na řadič dat na panelu připojení v části **řadiče ARC** rozbalte položku panely a vyberte možnost **Spravovat**.

Tady si můžete prohlédnout podrobnosti o prostředku řadiče dat, jako je název, oblast, režim připojení, skupina prostředků, předplatné, koncový bod kontroleru a obor názvů.  Můžete zobrazit také seznam všech spravovaných databázových prostředků spravovaných řadičem dat.

Všimněte si, že rozložení se podobá tomu, co se vám může zobrazit v Azure Portal.

Pohodlným kliknutím na tlačítko + Nová instance můžete spustit vytvoření spravované instance SQL nebo PostgreSQL skupiny serverů s škálovatelným škálováním.

Kliknutím na tlačítko otevřít v Azure Portal můžete také otevřít Azure Portal v kontextu tohoto kontroleru dat.

## <a name="view-the-sql-managed-instance-dashboards"></a>Zobrazit řídicí panely spravované instance SQL

Pokud jste vytvořili nějaké spravované instance SQL, můžete je zobrazit na panelu připojení v panelu připojení v okně řadiče dat Azure rozbalitelné panely pod řadičem dat, který je spravuje.

Chcete-li zobrazit řídicí panel spravované instance SQL pro danou instanci, klikněte pravým tlačítkem na instanci a vyberte možnost spravovat.

Panel připojení se zobrazí vpravo a zobrazí výzvu k přihlášení a heslo pro připojení k této instanci SQL. Pokud znáte informace o připojení, můžete je zadat a kliknout na připojit.  Pokud si nejste jisti, můžete kliknout na zrušit.  V obou případech se po zavření panelu připojení zobrazí řídicí panel.

Na kartě Přehled můžete zobrazit podrobnosti o spravované instanci SQL, například skupinu prostředků, řadič dat, ID předplatného, stav, oblast a další.  Můžete také zobrazit odkaz, na který můžete kliknout a přejít tak do řídicích panelů Grafana nebo Kibana v kontextu této spravované instance SQL.

Pokud se můžete připojit k instanci SQL Manage, můžete zobrazit další informace tady.

Můžete odstranit spravovanou instanci SQL z tohoto místa nebo otevřít Azure Portal pro zobrazení spravované instance SQL v Azure Portal.

Pokud kliknete na kartu připojovací řetězce vlevo, zobrazí se seznam předem konstruovaných připojovacích řetězců pro tuto spravovanou instanci SQL, který usnadňuje kopírování a vkládání do různých dalších aplikací nebo kódu.

## <a name="view-the-postgresql-hyperscale-server-group-dashboards"></a>Zobrazení řídicích panelů skupin serverů s PostgreSQL a škálováním

Pokud jste vytvořili některé PostgreSQL skupiny serverů s škálovatelným škálováním, můžete je zobrazit na panelu připojení v panelu připojení v rozevíracím seznamu řadiče dat Azure pod řídicím panelem, který je spravuje.

Chcete-li zobrazit řídicí panel skupiny serverů PostgreSQL s škálovatelným škálováním pro danou skupinu serverů, klikněte pravým tlačítkem na skupinu serverů a vyberte možnost spravovat.

Na kartě Přehled můžete zobrazit podrobnosti o skupině serverů, jako je například skupina prostředků, řadič dat, ID předplatného, stav, oblast a další.  Můžete také zobrazit odkaz, na který můžete kliknout a přejít tak do řídicích panelů Grafana nebo Kibana v kontextu této skupiny serverů.

Můžete odstranit skupinu serverů z tohoto umístění nebo otevřít Azure Portal pro zobrazení skupiny serverů v Azure Portal.

Pokud kliknete na kartu připojovací řetězce vlevo, zobrazí se seznam předem konstruovaných připojovacích řetězců pro tuto skupinu serverů, které usnadňují kopírování a vkládání do různých dalších aplikací nebo kódu.

Pokud na levé straně kliknete na kartu vlastnosti, uvidíte další podrobnosti.

Pokud kliknete na kartu stav prostředku vlevo, můžete zobrazit aktuální stav této skupiny serverů na nejvyšší úrovni.

Pokud kliknete na kartu Diagnostika a řešení problémů vlevo, můžete spustit Poznámkový blok pro řešení potíží s PostgreSQL.

Pokud kliknete na kartu nová žádost o podporu vlevo, můžete spustit Azure Portal v kontextu do skupiny serverů a vytvořit žádost o podporu Azure odtud.
