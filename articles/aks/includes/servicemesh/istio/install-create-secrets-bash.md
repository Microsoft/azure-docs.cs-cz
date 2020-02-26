---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: e8b94b4adc39de81dbdf17dfd24fcff39532b482
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593939"
---
### <a name="add-grafana-secret"></a>Přidat tajný klíč Grafana

Nahraďte `REPLACE_WITH_YOUR_SECURE_PASSWORD` token heslem a spusťte následující příkazy:

```bash
GRAFANA_USERNAME=$(echo -n "grafana" | base64)
GRAFANA_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE
EOF
```

### <a name="add-kiali-secret"></a>Přidat tajný klíč Kiali

Nahraďte `REPLACE_WITH_YOUR_SECURE_PASSWORD` token heslem a spusťte následující příkazy:

```bash
KIALI_USERNAME=$(echo -n "kiali" | base64)
KIALI_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE
EOF
```