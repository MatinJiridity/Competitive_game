DAO: {
	Player(id, name, email, password)
	Game(id, players, status)
	PaymentHash(id, hash)
} 

EndPoints{

	Shared-Headers: {
		Token
		Credential
	}

	getAllRockPaperGames: {
		Method: GET
		Parameters:{}
		Description: {
		این تابع تمامی بازی های rock paper را برمیگرداند
		}
	}

	joinToRockPaperGame: {
		Method: POST
		Parameters:{
			gameId,
			playerId,
			paymentHash,
			playerName,
		}
		Description: {
		این اندپوینت باید زمانی قابل دسرس باشد که کلاینت به سوکت کانکت شده باشد در غیر این صورت قابل کال شدن نیست چون در صورت کانکت نبودن سوکت کارایی ندارد
		این برای بازیکنی هست که قراره به یک بازی جوین بشه و در این تابع باید ابتدا هش پرداخت کاربر چک شود اگر هش اطلاعاتی دارد و هش قبلا مورد استفاده قرار نگرفته است به مرحله بعد میرود
		در دیتابیس در ستون Game و در فیلد players ایدی ارسال شده بازیکن پوش میشود
		و فیلد status بازی به started در می آید
		در ستون paymentHashes هش ارسال شده درج میشود
		در سوکت و در کانال rockPaperChanged رکورد جدید بازی ارسال میشود
		}

	}

	createRockPaperGame: {
		Method: POST
		Parameters: {
			gameId,
			playerId,
			paymentHash,
			gameRound,
			playerName,
		}
		Description: {
		این اندپوینت باید زمانی قابل دسرس باشد که کلاینت به سوکت کانکت شده باشد در غیر این صورت قابل کال شدن نیست چون در صورت کانکت نبودن سوکت کارایی ندارد
		این برای بازیکنی هست که قراره به یک بازی را بسازد  و در این تابع باید ابتدا هش پرداخت کاربر چک شود اگر هش اطلاعاتی دارد و هش قبلا مورد استفاده قرار نگرفته است به مرحله بعد میرود
		در ستون بازی ها یک بازی جدید با مشخصات ارسال شده ثبت میشود 
		هش ارسال شده در ستون هش ها ثبت میشود 
		در کانال rockPaperAdded بازی ارسال میشود
		}
	}
}

Socket channels {

	rockPaperAdded {
		Type: BROADCAST
		Parameters: *Game
		Description: {
		به تمامی بازیکن ها این گیم ورودی را ارسال میکند این کانال در اندپوینت  createRockPaperGame اجرا میشود 
		}
	}
	rockPaperChanged {
		Type: BROADCAST
		Parameters: playerId, paymentHash, gameId
		Description: {
		در ستون بازی ها توسط ایدی بازی ارسال شده مقدار playerId را push میکنیم
		ودر ستون paymentHashs هش تراکنش را ثبت میکنیم
		اکشن start به کانال با ایدی بازی ارسال میشود
		}
	}
	
	gameEmitter {
		Type: ROOM 
		Parameter: gameId, action
		Description: {
		ابتدا چک میکنیم که اجرا در ۵ ثانیه اول از شروع راند است یا خیر 
		اگر در صحیح است به لیست اکشن ها اضافه میشود در غیر صورت به لیست وارنینگ ها کاربر اضافه میشود
		چک میشود اگر وارنینگ ها به ۳ تا رسیده امتیاز بازیکن این راند را باخته است در غیر این صورت به لیست اکشن ها اضافه میشود
		}
	}


}