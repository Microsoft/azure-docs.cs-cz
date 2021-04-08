---
title: Jak vybrat vstupní zvukové zařízení pomocí sady Speech SDK
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak vybrat zařízení pro vstup zvuku v sadě Speech SDK (C++, C#, Python, objektiv-C, Java, JavaScript) získáním ID zvukových zařízení připojených k systému.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: chlandsi
ms.custom: devx-track-js
ms.openlocfilehash: 48316d571eac835dd5d4ec7d225048f4fdcdf237
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "95026603"
---
# <a name="how-to-select-an-audio-input-device-with-the-speech-sdk"></a>Postupy: Výběr zvukového vstupního zařízení pomocí sady Speech SDK

1.3.0 verze sady Speech SDK zavádí rozhraní API pro výběr zvukového vstupu. Tento článek popisuje, jak získat ID zvukových zařízení připojených k systému. Ty pak můžete použít v sadě Speech SDK nakonfigurováním zvukového zařízení prostřednictvím `AudioConfig` objektu:

```C++
audioConfig = AudioConfig.FromMicrophoneInput("<device id>");
```

```cs
audioConfig = AudioConfig.FromMicrophoneInput("<device id>");
```

```Python
audio_config = AudioConfig(device_name="<device id>");
```

```objc
audioConfig = AudioConfiguration.FromMicrophoneInput("<device id>");
```

```Java
audioConfig = AudioConfiguration.fromMicrophoneInput("<device id>");
```

```JavaScript
audioConfig = AudioConfiguration.fromMicrophoneInput("<device id>");
```

> [!Note]
> Používání mikrofonu není k dispozici pro JavaScript běžící v Node.js

## <a name="audio-device-ids-on-windows-for-desktop-applications"></a>ID zvukových zařízení ve Windows pro aplikace klasické pracovní plochy

[Řetězce ID koncového bodu](/windows/desktop/CoreAudio/endpoint-id-strings) zvukového zařízení lze načíst z [`IMMDevice`](/windows/desktop/api/mmdeviceapi/nn-mmdeviceapi-immdevice) objektu ve Windows pro aplikace klasické pracovní plochy.

Následující ukázka kódu ukazuje, jak ho použít k zobrazení výčtu zvukových zařízení v jazyce C++:

```cpp
#include <cstdio>
#include <mmdeviceapi.h>

#include <Functiondiscoverykeys_devpkey.h>

const CLSID CLSID_MMDeviceEnumerator = __uuidof(MMDeviceEnumerator);
const IID IID_IMMDeviceEnumerator = __uuidof(IMMDeviceEnumerator);

constexpr auto REFTIMES_PER_SEC = (10000000 * 25);
constexpr auto REFTIMES_PER_MILLISEC = 10000;

#define EXIT_ON_ERROR(hres)  \
              if (FAILED(hres)) { goto Exit; }
#define SAFE_RELEASE(punk)  \
              if ((punk) != NULL)  \
                { (punk)->Release(); (punk) = NULL; }

void ListEndpoints();

int main()
{
    CoInitializeEx(NULL, COINIT_MULTITHREADED);
    ListEndpoints();
}

//-----------------------------------------------------------
// This function enumerates all active (plugged in) audio
// rendering endpoint devices. It prints the friendly name
// and endpoint ID string of each endpoint device.
//-----------------------------------------------------------
void ListEndpoints()
{
    HRESULT hr = S_OK;
    IMMDeviceEnumerator *pEnumerator = NULL;
    IMMDeviceCollection *pCollection = NULL;
    IMMDevice *pEndpoint = NULL;
    IPropertyStore *pProps = NULL;
    LPWSTR pwszID = NULL;

    hr = CoCreateInstance(CLSID_MMDeviceEnumerator, NULL, CLSCTX_ALL, IID_IMMDeviceEnumerator, (void**)&pEnumerator);
    EXIT_ON_ERROR(hr);

    hr = pEnumerator->EnumAudioEndpoints(eCapture, DEVICE_STATE_ACTIVE, &pCollection);
    EXIT_ON_ERROR(hr);

    UINT  count;
    hr = pCollection->GetCount(&count);
    EXIT_ON_ERROR(hr);

    if (count == 0)
    {
        printf("No endpoints found.\n");
    }

    // Each iteration prints the name of an endpoint device.
    PROPVARIANT varName;
    for (ULONG i = 0; i < count; i++)
    {
        // Get pointer to endpoint number i.
        hr = pCollection->Item(i, &pEndpoint);
        EXIT_ON_ERROR(hr);

        // Get the endpoint ID string.
        hr = pEndpoint->GetId(&pwszID);
        EXIT_ON_ERROR(hr);

        hr = pEndpoint->OpenPropertyStore(
            STGM_READ, &pProps);
        EXIT_ON_ERROR(hr);

        // Initialize container for property value.
        PropVariantInit(&varName);

        // Get the endpoint's friendly-name property.
        hr = pProps->GetValue(PKEY_Device_FriendlyName, &varName);
        EXIT_ON_ERROR(hr);

        // Print endpoint friendly name and endpoint ID.
        printf("Endpoint %d: \"%S\" (%S)\n", i, varName.pwszVal, pwszID);

        CoTaskMemFree(pwszID);
        pwszID = NULL;
        PropVariantClear(&varName);
    }

Exit:
    CoTaskMemFree(pwszID);
    pwszID = NULL;
    PropVariantClear(&varName);
    SAFE_RELEASE(pEnumerator);
    SAFE_RELEASE(pCollection);
    SAFE_RELEASE(pEndpoint);
    SAFE_RELEASE(pProps);
}
```

V jazyce C# se knihovna [NAudio](https://github.com/naudio/NAudio) dá použít pro přístup k rozhraní CoreAudio API a k zobrazení výčtu zařízení následujícím způsobem:

```cs
using System;

using NAudio.CoreAudioApi;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            var enumerator = new MMDeviceEnumerator();
            foreach (var endpoint in
                     enumerator.EnumerateAudioEndPoints(DataFlow.Capture, DeviceState.Active))
            {
                Console.WriteLine("{0} ({1})", endpoint.FriendlyName, endpoint.ID);
            }
        }
    }
}
```

ID ukázkového zařízení je `{0.0.1.00000000}.{5f23ab69-6181-4f4a-81a4-45414013aac8}` .

## <a name="audio-device-ids-on-uwp"></a>ID zvukových zařízení na UWP

Na Univerzální platforma Windows (UWP) lze pomocí vlastnosti odpovídajícího objektu získat vstupní zvuková zařízení `Id()` [`DeviceInformation`](/uwp/api/windows.devices.enumeration.deviceinformation) .

Následující ukázky kódu ukazují, jak to provést v jazyce C++ a C#:

```cpp
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Devices.Enumeration.h>

using namespace winrt::Windows::Devices::Enumeration;

void enumerateDeviceIds()
{
    auto promise = DeviceInformation::FindAllAsync(DeviceClass::AudioCapture);

    promise.Completed(
        [](winrt::Windows::Foundation::IAsyncOperation<DeviceInformationCollection> const& sender,
           winrt::Windows::Foundation::AsyncStatus /* asyncStatus */ ) {
        auto info = sender.GetResults();
        auto num_devices = info.Size();

        for (const auto &device : info)
        {
            std::wstringstream ss{};
            ss << "looking at device (of " << num_devices << "): " << device.Id().c_str() << "\n";
            OutputDebugString(ss.str().c_str());
        }
    });
}
```

```cs
using Windows.Devices.Enumeration;
using System.Linq;

namespace helloworld {
    private async void EnumerateDevices()
    {
        var devices = await DeviceInformation.FindAllAsync(DeviceClass.AudioCapture);

        foreach (var device in devices)
        {
            Console.WriteLine($"{device.Name}, {device.Id}\n");
        }
    }
}
```

ID ukázkového zařízení je `\\\\?\\SWD#MMDEVAPI#{0.0.1.00000000}.{5f23ab69-6181-4f4a-81a4-45414013aac8}#{2eef81be-33fa-4800-9670-1cd474972c3f}` .

## <a name="audio-device-ids-on-linux"></a>ID zvukových zařízení v systému Linux

ID zařízení se vyberou pomocí standardních ID zařízení ALSA.

ID vstupů připojených k systému jsou obsažena ve výstupu příkazu `arecord -L` .
Alternativně je lze získat pomocí [knihovny jazyka C ALSA](https://www.alsa-project.org/alsa-doc/alsa-lib/).

ID vzorků jsou `hw:1,0` a `hw:CARD=CC,DEV=0` .

## <a name="audio-device-ids-on-macos"></a>ID zvukových zařízení v macOS

Následující funkce implementovaná v cíli – C vytvoří seznam názvů a ID zvukových zařízení připojených k počítači Mac.

`deviceUID`Řetězec se používá k identifikaci zařízení v sadě Speech SDK pro MacOS.

```objc
#import <Foundation/Foundation.h>
#import <CoreAudio/CoreAudio.h>

CFArrayRef CreateInputDeviceArray()
{
    AudioObjectPropertyAddress propertyAddress = {
        kAudioHardwarePropertyDevices,
        kAudioObjectPropertyScopeGlobal,
        kAudioObjectPropertyElementMaster
    };

    UInt32 dataSize = 0;
    OSStatus status = AudioObjectGetPropertyDataSize(kAudioObjectSystemObject, &propertyAddress, 0, NULL, &dataSize);
    if (kAudioHardwareNoError != status) {
        fprintf(stderr, "AudioObjectGetPropertyDataSize (kAudioHardwarePropertyDevices) failed: %i\n", status);
        return NULL;
    }

    UInt32 deviceCount = (uint32)(dataSize / sizeof(AudioDeviceID));

    AudioDeviceID *audioDevices = (AudioDeviceID *)(malloc(dataSize));
    if (NULL == audioDevices) {
        fputs("Unable to allocate memory", stderr);
        return NULL;
    }

    status = AudioObjectGetPropertyData(kAudioObjectSystemObject, &propertyAddress, 0, NULL, &dataSize, audioDevices);
    if (kAudioHardwareNoError != status) {
        fprintf(stderr, "AudioObjectGetPropertyData (kAudioHardwarePropertyDevices) failed: %i\n", status);
        free(audioDevices);
        audioDevices = NULL;
        return NULL;
    }

    CFMutableArrayRef inputDeviceArray = CFArrayCreateMutable(kCFAllocatorDefault, deviceCount, &kCFTypeArrayCallBacks);
    if (NULL == inputDeviceArray) {
        fputs("CFArrayCreateMutable failed", stderr);
        free(audioDevices);
        audioDevices = NULL;
        return NULL;
    }

    // Iterate through all the devices and determine which are input-capable
    propertyAddress.mScope = kAudioDevicePropertyScopeInput;
    for (UInt32 i = 0; i < deviceCount; ++i) {
        // Query device UID
        CFStringRef deviceUID = NULL;
        dataSize = sizeof(deviceUID);
        propertyAddress.mSelector = kAudioDevicePropertyDeviceUID;
        status = AudioObjectGetPropertyData(audioDevices[i], &propertyAddress, 0, NULL, &dataSize, &deviceUID);
        if (kAudioHardwareNoError != status) {
            fprintf(stderr, "AudioObjectGetPropertyData (kAudioDevicePropertyDeviceUID) failed: %i\n", status);
            continue;
        }

        // Query device name
        CFStringRef deviceName = NULL;
        dataSize = sizeof(deviceName);
        propertyAddress.mSelector = kAudioDevicePropertyDeviceNameCFString;
        status = AudioObjectGetPropertyData(audioDevices[i], &propertyAddress, 0, NULL, &dataSize, &deviceName);
        if (kAudioHardwareNoError != status) {
            fprintf(stderr, "AudioObjectGetPropertyData (kAudioDevicePropertyDeviceNameCFString) failed: %i\n", status);
            continue;
        }

        // Determine if the device is an input device (it is an input device if it has input channels)
        dataSize = 0;
        propertyAddress.mSelector = kAudioDevicePropertyStreamConfiguration;
        status = AudioObjectGetPropertyDataSize(audioDevices[i], &propertyAddress, 0, NULL, &dataSize);
        if (kAudioHardwareNoError != status) {
            fprintf(stderr, "AudioObjectGetPropertyDataSize (kAudioDevicePropertyStreamConfiguration) failed: %i\n", status);
            continue;
        }

        AudioBufferList *bufferList = (AudioBufferList *)(malloc(dataSize));
        if (NULL == bufferList) {
            fputs("Unable to allocate memory", stderr);
            break;
        }

        status = AudioObjectGetPropertyData(audioDevices[i], &propertyAddress, 0, NULL, &dataSize, bufferList);
        if (kAudioHardwareNoError != status || 0 == bufferList->mNumberBuffers) {
            if (kAudioHardwareNoError != status)
                fprintf(stderr, "AudioObjectGetPropertyData (kAudioDevicePropertyStreamConfiguration) failed: %i\n", status);
            free(bufferList);
            bufferList = NULL;
            continue;
        }

        free(bufferList);
        bufferList = NULL;

        // Add a dictionary for this device to the array of input devices
        CFStringRef keys    []  = { CFSTR("deviceUID"),     CFSTR("deviceName")};
        CFStringRef values  []  = { deviceUID,              deviceName};

        CFDictionaryRef deviceDictionary = CFDictionaryCreate(kCFAllocatorDefault,
                                                              (const void **)(keys),
                                                              (const void **)(values),
                                                              2,
                                                              &kCFTypeDictionaryKeyCallBacks,
                                                              &kCFTypeDictionaryValueCallBacks);

        CFArrayAppendValue(inputDeviceArray, deviceDictionary);

        CFRelease(deviceDictionary);
        deviceDictionary = NULL;
    }

    free(audioDevices);
    audioDevices = NULL;

    // Return a non-mutable copy of the array
    CFArrayRef immutableInputDeviceArray = CFArrayCreateCopy(kCFAllocatorDefault, inputDeviceArray);
    CFRelease(inputDeviceArray);
    inputDeviceArray = NULL;

    return immutableInputDeviceArray;
}
```

Například UID vestavěného mikrofonu je `BuiltInMicrophoneDevice` .

## <a name="audio-device-ids-on-ios"></a>ID zvukových zařízení v iOS

Výběr zvukového zařízení pomocí sady Speech SDK není v iOS podporován. Aplikace používající sadu SDK ale mohou ovlivnit zvukové směrování prostřednictvím [`AVAudioSession`](https://developer.apple.com/documentation/avfoundation/avaudiosession?language=objc) rozhraní.

Například instrukce

```objc
[[AVAudioSession sharedInstance] setCategory:AVAudioSessionCategoryRecord
    withOptions:AVAudioSessionCategoryOptionAllowBluetooth error:NULL];
```

Povolí použití sluchátek s mikrofonem Bluetooth pro aplikaci s podporou řeči.

## <a name="audio-device-ids-in-javascript"></a>ID zvukových zařízení v JavaScriptu

V JavaScriptu lze pomocí metody [MediaDevices. enumerateDevices ()](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/enumerateDevices) vytvořit výčet mediálních zařízení a vyhledat ID zařízení, které se má předat `fromMicrophone(...)` .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte naše ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také

- [Přizpůsobení akustických modelů](./how-to-custom-speech-train-model.md)
- [Přizpůsobení jazykových modelů](./how-to-custom-speech-train-model.md)