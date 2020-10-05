---
title: 'Rychlý Start: sada Speech SDK C++ (Linux) nastavení platformy – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto průvodce můžete nastavit platformu pro C++ v systému Linux se sadou Speech Service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 099c835ae2047538c882414660f699341af60b8b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "83673250"
---
V této příručce se dozvíte, jak nainstalovat [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro Linux.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Požadavky na systém

Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 7/8, CentOS 7/8)

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto rychlého startu budete potřebovat:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* Podporované platformy Linux budou vyžadovat nainstalovaná konkrétní knihovny ( `libssl` pro podporu SSL (Secure Sockets Layer `libasound2` ) a pro podporu zvuku. Pro příkazy potřebné k instalaci správných verzí těchto knihoven použijte níže uvedenou distribuci.

   * V Ubuntu:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * V Debian 9:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

   * V RHEL/CentOS:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - V RHEL/CentOS 7 postupujte podle pokynů, [jak nakonfigurovat RHEL/CentOS 7 pro sadu Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - V RHEL/CentOS 8 postupujte podle pokynů, [jak nakonfigurovat OpenSSL pro Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]

## <a name="next-steps"></a>Další kroky

[!INCLUDE [windows](../quickstart-list.md)]
