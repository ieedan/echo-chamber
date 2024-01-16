<script lang="ts">
	import { Hero, Center, Input, Divider, Button, SkeletonContainer, Skeleton, Text, Spacer } from "geist-ui-svelte";
	import { onMount } from "svelte";

	type Message = {
		content: string;
		time: Date;
		from: "user" | "system";
	};

	const LOADING_TIME = 500;
	const MESSAGES_KEY = "messages";

	let messages: Message[];

	let message: string = "";

	let sending = false;
	let loading = true;

	const getMessages = () => {
		return new Promise<void>((res) =>
			setTimeout(() => {
				messages = JSON.parse(localStorage.getItem(MESSAGES_KEY) ?? "[]") ?? [];
				res();
			}, LOADING_TIME)
		);
	};

	const sendMessage = () => {
		sending = true;

		setTimeout(() => {
            const returnMessage = checkForI(message);

			messages = [...messages, { content: message, time: new Date(), from: "user" }];

            localStorage.setItem(MESSAGES_KEY, JSON.stringify(messages));

			message = "";

			sending = false;

            setTimeout(() => {
                messages = [...messages, { content: returnMessage, time: new Date(), from: "system" }];

                localStorage.setItem(MESSAGES_KEY, JSON.stringify(messages));
            }, 100)
		}, 500);
	};

    const checkForI = (m: string) => {
        let s = m;
        let i = 0;
        while (i < s.length) {
            const char = s[i];
            if (char.toLowerCase() == "i") {
                if ((s[i - 1] == undefined || s[i - 1] == " ") && s[i + 1] == " ") {
                    s = s.slice(0, i) + "you" + s.slice(i + 1);
                }
            }
            
            i++;
        }

        return s;
    }

	onMount(() => {
		getMessages().then(() => {
			loading = false;
		});
	});
</script>

<Hero exclusionHeight={53} class="flex flex-col justify-between">
	<div class="flex-grow py-2 w-full max-w-6xl">
		{#if loading}
			<SkeletonContainer loading>
				<div class="flex flex-col w-full gap-4">
					<Skeleton class="w-48 lg:w-96 h-10 rounded-xl place-self-end" />
					<Skeleton class="w-48 lg:w-96 h-10 rounded-xl place-self-start" />
					<Skeleton class="w-48 lg:w-96 h-10 rounded-xl place-self-start" />
					<Skeleton class="w-48 lg:w-96 h-10 rounded-xl place-self-end" />
					<Skeleton class="w-48 lg:w-96 h-10 rounded-xl place-self-end" />
					<Skeleton class="w-48 lg:w-96 h-10 rounded-xl place-self-start" />
				</div>
			</SkeletonContainer>
		{:else}
			<div class="flex flex-col w-full gap-4 overflow-y-auto">
				{#each messages as message}
					<div
						data-sender={message.from}
						class="data-[sender='system']:place-self-start data-[sender='user']:place-self-end
                        data-[sender='user']:bg-blue-600 px-4 py-2 rounded-xl data-[sender='system']:dark:bg-gray-900 
                        data-[sender='system']:bg-gray-100 data-[sender='system']:rounded-bl-none
                         data-[sender='user']:rounded-br-none group" 
					>
                        <p class="text-gray-0 text-lg group-data-[sender='system']:text-gray-999 
                        group-data-[sender='system']:dark:text-gray-0">{message.content}</p>
					</div>
				{/each}
			</div>
		{/if}
        <Spacer h={55}/>
	</div>
	<div class="w-full fixed bottom-0 bg-gray-0 dark:bg-gray-999">
		<Divider />
		<Center class="w-full p-2">
			<form on:submit|preventDefault={sendMessage} class="w-full max-w-6xl gap-2 flex place-items-center">
				<div class="flex-grow">
					<Input bind:value={message} bind:readonly={sending} placeholder="Send a message..." size="xl" width="100%"></Input>
				</div>
				<Button type="submit" color="success-light" bind:loading={sending}>Send</Button>
			</form>
		</Center>
	</div>
</Hero>
