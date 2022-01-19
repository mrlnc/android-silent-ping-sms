# Android Silent SMS Ping

[<img src="https://f-droid.org/badge/get-it-on.png" alt="Get it on F-Droid" height="60">](https://f-droid.org/app/com.itds.sms.ping) [<img src="getapkfromgithub.png" alt="Download APK from GitHub" height="60">](https://github.com/itds-consulting/android-silent-ping-sms/releases)

  - Does not require rooted device
  - SMS will not be delivered as standard SMS, target user will receive no visual notification about SMS being received

## Description

  - Payload looks like this: `byte[]{0x0A, 0x06, 0x03, (byte) 0xB0, (byte) 0xAF, (byte) 0x82, 0x03, 0x06, 0x6A, 0x00, 0x05}`
  - Full SMS PDU looks like this: `03050020 01f61fe0c91246066833682000412 06050423f00000 0a0603b0af8203066a0005`
    - Where first segment is SMSC (SMS Center), second is user-defined data SMS, last segment is payload specified in userspace
    - Using this payload, remote mobile station (baseband) will not deliver or correctly process the SMS, will only provide ACK (delivery report)

For some more context, this is the hex dump with a dummy phone number `491111111`:
```
0000   61 01 09 91 94 11 11 11 f1 00 04 12 06 05 04 23
0010   f0 00 00 0a 06 03 b0 af 82 03 06 6a 00 05
```

Wireshark dissection:
```
GSM SMS TPDU (GSM 03.40) SMS-SUBMIT
    0... .... = TP-RP: TP Reply Path parameter is not set in this SMS SUBMIT/DELIVER
    .1.. .... = TP-UDHI: The beginning of the TP UD field contains a Header in addition to the short message
    ..1. .... = TP-SRR: A status report is requested
    ...0 0... = TP-VPF: TP-VP field not present (0)
    .... .0.. = TP-RD: Instruct SC to accept duplicates
    .... ..01 = TP-MTI: SMS-SUBMIT (1)
    TP-MR: 1
    TP-Destination-Address - (491111111)
        Length: 9 address digits
        1... .... = Extension: No extension
        .001 .... = Type of number: International (1)
        .... 0001 = Numbering plan: ISDN/telephone (E.164/E.163) (1)
        TP-DA Digits: 491111111
        E.164 number (MSISDN): 491111111
            Country Code: Germany (Federal Republic of) (49)
    TP-PID: 0
        00.. .... = Defines formatting for subsequent bits: 0x0
        ..0. .... = Telematic interworking: no telematic interworking, but SME-to-SME protocol
        ...0 0000 = The SM-AL protocol being used between the SME and the MS: 0
    TP-DCS: 4
        00.. .... = Coding Group Bits: General Data Coding indication (0)
        ..0. .... = Text: Not compressed
        ...0 .... = Message Class: Reserved, no message class
        .... 01.. = Character Set: 8 bit data (0x1)
        .... ..00 = Message Class: Class 0 (0x0)
    TP-User-Data-Length: (18) depends on Data-Coding-Scheme
    TP-User-Data
        User-Data Header
            User Data Header Length: 6
            IE: Application port addressing scheme, 16 bit address (SMS Control)
                Information Element Identifier: 0x05
                Length: 4
                Destination port: UDP/TCP port numbers assigned by IANA without the need to refer to 3GPP (9200)
                Originator port: UDP/TCP port numbers assigned by IANA without the need to refer to 3GPP (0)
        Wireless Session Protocol, Method: Push (0x06), Content-Type: application/vnd.wap.slc
        WAP Binary XML, Version: 1.3, Public ID: "-//WAPFORUM//DTD SL 1.0//EN (Service Loading 1.0)"
            Version: 1.3 (0x03)
            Public Identifier (known): -//WAPFORUM//DTD SL 1.0//EN (Service Loading 1.0) (0x00000006)
            Character Set: UTF-8 (106)
            String table: 0 bytes
            Data representation
                Level | State | Codepage | WBXML Token Description         | Rendering
                        2 | Tag   | T   0    |   Known Tag 0x05           (..) |      <sl />
```

## Links

  - 3GPP 23.040 (originally GSM 03.40) https://en.wikipedia.org/wiki/GSM_03.40
  - 3GPP 23.038 (originally GSM 03.38) https://en.wikipedia.org/wiki/GSM_03.38

## Build

  - Compile it yourself and install on Android connected device
    - ./gradle clean installDebug
    
## License

   The project is licensed under the [GNU General Public License version 3 (or newer)](https://github.com/itds-consulting/android-silent-ping-sms/blob/master/LICENSE)

