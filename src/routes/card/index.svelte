<script>
  import WideCard from "../../components/Card/WideCard.svelte";
  import BackgroundFrame from "../../components/Frame/BackgroundFrame.svelte";
  import KakaoButton from "../../components/Button/KakaoButton.svelte";
  import CopyUrlButton from "../../components/Button/CopyUrlButton.svelte";
  import Toast from "../../components/Toast/BasicToast.svelte";
  import no_resource from "../../../static/no_resource.png";
  import { getContext } from "svelte";
  import { stores } from "@sapper/app";
  import { cardStore } from "../../store/card.store";
  import { getRandomImage } from "../../utils/pickCards";

  const {card: cardImage, color} = getRandomImage();
  const cardsContext = getContext("cards");
  const { page } = stores();
  let card;
  if ($page.query.id) {
    const foundCard = (cardsContext.contents || []).find((item) => item.showId === $page.query.id);
    if (foundCard) {
      card = { ...foundCard, isEmpty: false };
    } else {
      card = { isEmpty: true };
    }
  } else {
    if (Object.entries($cardStore).length) {
      card = $cardStore;
    } else {
      card = { isEmpty: true };
    }
  }
  
  let isCopied = false;
  let isShowToast = false;
  let toastShowId;

  const handleClickCopyUrl = () => {
    if(isShowToast){
      return false;
    }
    else {
      isShowToast = true;
    
      toastShowId = setTimeout(() => {
        isShowToast = false;
      },1500);
    }
  };
</script>

<section class="frame_wrapper">
  <BackgroundFrame className="override_background_frame">
    {#if !card.isEmpty}
      <div class="card_wrapper">
        <WideCard 
          title={card.title} 
          content={card.text} 
          imageUrl={cardImage} 
          textColor={
            ["yellow"].includes(color) ? "blue": 
            ["sky"].includes(color) ? "#483d8b" :
            ["pink", "purple"].includes(color) ? "#063970" : "white"
          }
        />
      </div>
      <div id="sns_button_container">
        <KakaoButton
          className="kakao_button"
          description={"#성경 #하나님 #2022년 #감사 #기도 #코로나 #온택트"}
          title={"2022년 성경 말씀 나누기"}
          imageUrl={"https://hanmoa-bucket.s3.ap-northeast-2.amazonaws.com/hispick/introduce_illustration.png"}
          link={`https://2022word.com/card?id=${$page.query.id || card.showId}`}
        />
        <CopyUrlButton 
          onAfterClick={handleClickCopyUrl}
          isCopied={isCopied} 
          link={`https://2022word.com/card?id=${$page.query.id || card.showId}`}
        />
      </div>
    {:else}
      <div class="no_resource" style="background: url({no_resource}) center/cover no-repeat;" />
      <div class="empty_description">메세지 함이 비어있네요!</div>
      <div class="empty_description">
        새로운 <a href="." rel="prefetch">말씀</a>을 요청해보세요
      </div>
    {/if}
    {#if isShowToast}
      <Toast title="복사 성공!" message="지인들과 나누어 보세요^^"/>
    {/if}
  </BackgroundFrame>
</section>

<style lang="scss">
  @keyframes fadein {
    from {
      opacity: 0;
    }
    to {
      opacity: 1;
    }
  }

  .empty_description a {
    text-decoration: none;
    color: blue;
  }
  .override_background_frame {
    margin: 0;
  }

  .frame_wrapper {
    position: relative;
    padding: 20px 0;
    margin: 10px;
    @media (max-width: 295px) {
      margin: 10px 0;
    }
  }

  .no_resource {
    width: 100%;
    max-width: 450px;
    padding-top: 100%;
  }

  .empty_description {
    text-align: center;
    font-size: 28px;

    > a {
      font-weight: 700;
    }
  }

  .card_wrapper {
    animation: fadein 3s;
    width: 280px;
    margin: 0 auto;
  }

  #sns_button_container{
    display: flex;
    justify-content: center;
    margin-left: 42px;
  }

  #sns_button_container :global(.kakao_button) {
    margin-right: 8px;
  }
</style>
