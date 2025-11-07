# Lab 4 WebSocket -- Project Report

## Description of Changes
During this lab, I completed the `onChat` test in `ElizaServerTest.kt` file. In 
order to do so, I completed the `onMessage` function of the `ComplexClient` class.

- Removed `@Disabled` annotation from the `onChat` test to enable it.
- Implemented `ComplexClient` function.
    ```kotlin
    fun onMessage(
        message: String,
        session: Session,
    ) {
        logger.info { "Client received: $message" }
        list.add(message)
        latch.countDown()
        // 5. COMPLETE if (expression) {
        // 6. COMPLETE   sentence
        // }
        val size = list.size
        if (size == 3) {
             session.asyncRemote.sendText("I am always feeling sad")
        }
    }
    ```
- Implemented `onChat` test, checking the response matches "Can you think of a specific example?"
    ```kotlin
    @Test
    fun onChat() {
        logger.info { "Test thread" }
        val latch = CountDownLatch(4)
        val list = mutableListOf<String>()
    
        val client = ComplexClient(list, latch)
        client.connect("ws://localhost:$port/eliza")
        latch.await()
        val size = list.size
        // 1. EXPLAIN WHY size = list.size IS NECESSARY: Because the value of list.size may vary right after the latch.await()
        // 2. REPLACE BY assertXXX expression that checks an interval; assertEquals must not be used;
        assertTrue(size in 4..5)
        // 3. EXPLAIN WHY assertEquals CANNOT BE USED AND WHY WE SHOULD CHECK THE INTERVAL: because the exact number of messages can vary slightly, it's not deterministic.
        // 4. COMPLETE assertEquals(XXX, list[XXX])
        assertEquals("Can you think of a specific example?", list[size - 1])
        logger.info { "1. I have received $size messages" }
    }
    ```
- Changed `CI.yml`: During my debugging, I realised that there was a line in `CI.yml` that wasn't allowing
the GitHub Workflow to run correctly. After looking up the error online, I realised there was a small typo 
on the code, so I changed the following lines:
    ```yml
        with:
          java-version: '21'
          distribution: 'temurin'     # Previously 'temuring'
    ```

## Technical Decisions
- Use the variable `size = list.size` in order to reduce the risk of that value changing while the tests are ongoing.
- Use of assertTrue(size in 4..5) to ensure the test stability and tolerancy of asynchronous message timing.

## Learning Outcomes
Before this laboratory, I didn't know about Eliza, and it was interesting to investigate about.
During this lab I also learned how to test WebSockets in Kotlin and Spring Boot, as well as the 
message handling for timing and variability in the responses.

## AI Disclosure
Grammarly and ChatGPT have been only used in order to improve the clarity of this report (~15%). 
