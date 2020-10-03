---
title: zahrnout soubor
description: zahrnout soubor
services: azure-netapp-files
author: b-juche
ms.service: azure-netapp-files
ms.topic: include
ms.date: 09/10/2019
ms.author: b-juche
ms.custom: include file
ms.openlocfilehash: e78be737ef8d191cddc2da0f738250ca04cae9a1
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91672103"
---
1. Zadejte předplatné, které bylo schváleno pro Azure NetApp Files:
    
    ```azurecli-interactive
    az account set --subscription <subscriptionId>
    ```

1. Zaregistrujte poskytovatele prostředků Azure: 
    
    ```azurecli-interactive
    az provider register --namespace Microsoft.NetApp --wait  
    ```