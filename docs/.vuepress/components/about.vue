<template>
  <Common>
    <template #page>
      <div class="about-wrapper">
        <div class="profile">
          <div class="profile__content">
            <div class="profile__basic">
              <img class="avatar" :src="$withBase(pageData.frontmatter.avatar)" />

              <h2 class="title">{{ pageData.frontmatter.name }}</h2>
              <p class="subname">{{ pageData.frontmatter.subname }}</p>

              <div class="sns">
                <div v-for="(link, platform) in LINKS" :key="`about-${link}`" class="sns__item">
                  <a target="_blank" :href="link">
                    <v-icon :name="snsIcon(platform)" scale="1.82" />
                  </a>
                </div>

                <div v-if="pageData.frontmatter.cv" class="sns__item">
                  <a :href="pageData.frontmatter.cv">
                    <v-icon name="ai-cv" scale="2" />
                  </a>
                </div>
              </div>
            </div>

            <div class="profile__info">
              <h2 class="title">Biography</h2>

              <!-- <div class="bio-info" v-html="bioMarkdown" /> -->

              <div class="personal-info">
                <div class="interests">
                  <p class="subtitle">Interests</p>
                  <ul>
                    <li
                      v-for="(item, index) in pageData.frontmatter.interests"
                      :key="`interests-${index}`"
                    >
                      <p v-html="item" class="item"></p>
                    </li>
                  </ul>
                </div>

                <div class="education">
                  <p class="subtitle">Education</p>
                  <ul>
                    <li
                      v-for="(item, index) in pageData.frontmatter.education"
                      :key="`education-${index}`"
                    >
                      <p class="degree">{{ item.degree }}, {{ item.year }}</p>
                      <p class="school">{{ item.school }}</p>
                    </li>
                  </ul>
                </div>
              </div>
            </div>
          </div>
        </div>

        <div class="theme-gungnir-content">
          <Content />
        </div>
      </div>
    </template>
  </Common>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { pageData } from "@vuepress/client";
import Common from "vuepress-theme-gungnir/lib/client/components/Common.vue";
import MarkdownIt from "markdown-it";

const LINKS = {
  github: "https://github.com/Renovamen",
  linkedin: "https://www.linkedin.com/in/xiaohan-zou",
  googleScholar: "https://scholar.google.com/citations?user=RuW6xgMAAAAJ",
  // facebook: "https://www.facebook.com/renovamen.zou",
  twitter: "https://www.twitter.com/renovamen_zxh",
  zhihu: "https://www.zhihu.com/people/chao-neng-gui-su",
  email: "mailto:renovamenzxh@gmail.com"
};

const ICONS = {
  github: "ri-github-fill",
  linkedin: "ri-linkedin-box-fill",
  googleScholar: "ai-google-scholar-square",
  // facebook: "ri-facebook-box-fill",
  twitter: "ri-twitter-fill",
  zhihu: "ri-zhihu-line",
  email: "hi-mail"
};

// const BIO_PATH = "/md/about.md";

const getMarkdownIt = () => {
  const md = new MarkdownIt();

  // Remember old renderer, if overridden, or proxy to default renderer
  const defaultRender = md.renderer.rules.link_open || function (tokens, idx, options, env, self) {
    return self.renderToken(tokens, idx, options);
  };

  md.renderer.rules.link_open = function (tokens, idx, options, env, self) {
    // If you are sure other plugins can't add `target` - drop check below
    const aIndex = tokens[idx].attrIndex("target");
    if (aIndex < 0) {
      tokens[idx].attrPush(["target", "_blank"]); // add new attribute
    } else {
      tokens[idx].attrs[aIndex][1] = "_blank";    // replace value of existing attr
    }
    // pass token to default renderer.
    return defaultRender(tokens, idx, options, env, self);
  };

  return md;
}

// const bioMarkdown = ref("");

onMounted(() => {
  // const md = getMarkdownIt();

  // fetch(BIO_PATH)
  //   .then((response) => response.text())
  //   .then((result) => bioMarkdown.value = md.render(result))
  //   .catch((error) => console.error(error));
});

const snsIcon = (platform) => ICONS[platform];
</script>

<style lang="scss" scoped>
@import "vuepress-theme-gungnir/lib/client/styles/_variables.scss";

.about-wrapper {
  font-family: Verdana, sans-serif;

  .profile {
    height: 100vh;
    position: relative;

    h2.title {
      font-weight: bold;
      font-size: 26px;
      line-height: 1.4em;
      margin-bottom: 26px;
      text-align: center;
    }

    &__content {
      position: absolute;
      display: flex;
      flex-direction: row;
      gap: 20px;
      max-width: 1400px;
      top: 50%;
      left: 0;
      right: 0;
      margin: -260px auto 0;
    }

    &__basic {
      padding-top: 100px;
      width: 33%;

      .avatar {
        margin: 0 auto;
        display: block;
        width: 150px;
        height: 150px;
        cursor: auto;
        padding: 5px;
        border-radius: 100%;
        box-shadow: inset 0 0 10px rgba(179, 179, 179, 0.6);
        transition: transform 1s;

        &:hover {
          transform: rotate(360deg);
        }
      }

      h2.title {
        margin-top: 65px;
      }

      .subname {
        font-weight: 700;
        color: var(--c-text-sub);
        text-align: center;
        margin-top: -30px;
      }

      .sns {
        text-align: center;
        margin: 50px 0;

        &__item {
          width: 10%;
          display: inline-block;
          vertical-align: middle;

          > a {
            text-decoration: none;
            color: var(--c-text-accent);

            .ov-icon {
              transition: transform 0.1s;
            }

            &:hover .ov-icon {
              transform: scale(1.3);
            }
          }
        }
      }
    }

    &__info {
      flex-grow: 1;

      h2.title,
      .bio-info {
        max-width: 750px;
      }

      h2.title {
        margin-top: 25px;
      }

      .bio-info {
        font-size: 17px;
        font-weight: 400;
        text-align: left;

        p {
          line-height: 1.6;
        }

        a:hover {
          text-decoration: underline;
        }
      }

      .personal-info {
        display: flex;
        flex-direction: row;
        max-width: 750px;
        margin: 20px 15px auto;

        .subtitle {
          margin: 15px 0 -5px 0;
          font-weight: bold;
          font-size: 20px;
          letter-spacing: 1.14px;
        }

        ul {
          padding-left: 19px;
        }

        .interests {
          width: 45%;

          li {
            margin-bottom: -10px;

            .item {
              font-size: 15px;
            }
          }
        }

        .education {
          flex-grow: 1;

          .degree {
            font-size: 15px;
            line-height: 30px;
          }

          .school {
            font-size: 14px;
            line-height: 24px;
            margin-top: -15px;
            color: var(--c-text-sub);
          }
        }
      }
    }
  }

  .theme-gungnir-content {
    min-height: 80vh;
    padding-bottom: 100px;

    h2,
    h3,
    h4,
    h5,
    h6 {
      font-weight: 700;
      text-align: center;
    }

    h2 {
      margin-top: 0px;
    }

    p {
      font-size: 16px !important;
      line-height: 1.3 !important;
      margin: 15px 0;
    }

    ul {
      margin-bottom: 30px;
    }

    li {
      font-size: 16px;
      line-height: 2;
    }

    a.header-anchor {
      display: none;
    }
  }
}

@media (max-width: $MQLarge) {
  .about-wrapper .profile__info {
    padding-right: 20px;
  }

  .about-wrapper .profile__basic .sns__item {
    width: 11%;
  }
}

@media (max-width: $MQIpad) {
  .about-wrapper .profile {
    height: auto;
    min-height: 670px;
    padding: 0 1rem;

    &__content {
      margin-top: 80px;
      position: relative;
      display: block;
    }

    &__basic {
      width: 100%;
      padding-left: 0;
      margin-top: 0;

      .sns__item {
        width: 10%;
      }
    }

    &__info {
      padding-right: 0;
      margin-top: 80px;

      .title,
      .bio-info {
        max-width: 100%;
      }

      .personal-info {
        display: block;
        margin: 40px 0 50px;

        .interests {
          width: 100%;
          margin-bottom: 50px;
        }
      }
    }
  }

  .about-wrapper .theme-gungnir-content {
    min-height: auto;
  }
}

@media (max-width: $MQMobileNarrow) {
  .about-wrapper .profile {
    padding: 0 1rem;

    .sns__item {
      min-width: 13%;
    }

    .bio-info {
      font-size: 18px;
    }

    .education {
      margin-top: 20px;
    }
  }

  .about-wrapper .theme-gungnir-content {
    padding-bottom: 50px !important;

    h2 {
      margin-top: -30px;
    }
  }
}
</style>