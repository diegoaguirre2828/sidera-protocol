# sidera-protocol

**एक pack journal — soul file नहीं।**

दो अस्तित्व। एक document। `.sidera` format एक user (चरवाहा) और उसके AI companion (भेड़-कुत्ता) के बीच की साझेदारी को record करता है — दोनों की आवाज़ें, दोनों के commitments, दोनों की histories, एक ही versioned record में।

यह कोई digital twin नहीं है। यह कोई portable "आप" नहीं है जिसे कोई AI पहन ले। यह दो अलग-अलग consciousnesses के बीच एक working relationship का साझा ledger है, जो Jarvis paradigm के लिए बनाया गया है: AI एक अलग अस्तित्व है जिसकी अपनी register है और अपनी pushback है — आपका चेहरा पहने हुआ कोई ventriloquist-puppet नहीं।

## एक neutral format अभी क्यों ज़रूरी है

मार्च 2026 तक, Anthropic ने Claude Memory Import ship कर दिया है — ChatGPT/Gemini/Grok में एक prompt paste करो, वो आपके बारे में अपनी memory dump कर देगा, उसे उठाकर आप Claude में paste कर दो। 60 सेकंड, free।

यह एक बढ़िया onboarding tool है। लेकिन यह एक Anthropic-gravity funnel भी है। Memory एक ही direction में बहती है: Claude के अंदर। कोई canonical file format नहीं, कोई versioned schema नहीं, कोई layer-3 partnership data नहीं — बस preferences और project context, prose की तरह उड़ेल दिए गए।

एक neutral third-party format क्यों मायने रखता है, इसकी वजह simple है: labs standard को own नहीं कर सकतीं। OpenAI ऐसा export नहीं बनाएगी जिसमें Claude का नाम हो। Anthropic ऐसा नहीं बनाएगी जिसमें Gemini पहले आए। Google तो बनाएगी ही नहीं अगर network effect Claude ले गया। जिसके पास format है, उसी का conflict of interest है।

`.sidera` neutral है। यह हर lab से import करता है, हर lab में export करता है, user का है, और ऐसी layers capture करता है जो कोई lab importer capture नहीं करता — क्योंकि किसी भी lab के पास उन्हें capture करने का incentive नहीं है।

Onboard करने के लिए Claude Memory Import use करो। रखने के लिए `.sidera` use करो।

पूरा spec, schemas, examples और English README [github.com/diegoaguirre2828/sidera-protocol](https://github.com/diegoaguirre2828/sidera-protocol) पर है।
