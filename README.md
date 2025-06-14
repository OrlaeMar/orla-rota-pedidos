import React, { useState } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import { Select, SelectTrigger, SelectValue, SelectContent, SelectItem } from "@/components/ui/select";

export default function Home() {
  const [loggedIn, setLoggedIn] = useState(false);
  const [user, setUser] = useState({ email: "", password: "", name: "" });
  const [loginData, setLoginData] = useState({ email: "", password: "", name: "" });
  const [postText, setPostText] = useState("");
  const [filter, setFilter] = useState("todos");
  const [posts, setPosts] = useState([
    {
      id: 1,
      user: "João Silva",
      content: "Vendo bicicleta usada, boa para trabalhar!",
      type: "venda",
      comments: [],
    },
    {
      id: 2,
      user: "Maria Oliveira",
      content: "Notícia: Novo mercado será inaugurado amanhã no centro!",
      type: "noticia",
      comments: [],
    },
  ]);
  const [commentText, setCommentText] = useState({});

  const handleLogin = () => {
    if (loginData.email && loginData.password && loginData.name) {
      setLoggedIn(true);
      setUser({ ...loginData });
    }
  };

  const handlePost = () => {
    if (postText.trim() !== "") {
      const newPost = {
        id: posts.length + 1,
        user: user.name,
        content: postText,
        type: postText.toLowerCase().includes("vendo") ? "venda" : "noticia",
        comments: [],
      };
      setPosts([newPost, ...posts]);
      setPostText("");
    }
  };

  const handleComment = (postId) => {
    if (commentText[postId]?.trim()) {
      setPosts((prev) =>
        prev.map((post) =>
          post.id === postId
            ? {
                ...post,
                comments: [...post.comments, { user: user.name, content: commentText[postId] }],
              }
            : post
        )
      );
      setCommentText({ ...commentText, [postId]: "" });
    }
  };

  const filteredPosts =
    filter === "todos" ? posts : posts.filter((p) => p.type === filter);

  return (
    <main className="max-w-xl mx-auto py-8 px-4">
      {!loggedIn ? (
        <Card className="mb-4">
          <CardContent className="space-y-4">
            <h1 className="text-xl font-bold">Criar Conta na Rede da Cidade</h1>
            <Input placeholder="Nome" onChange={(e) => setLoginData({ ...loginData, name: e.target.value })} />
            <Input placeholder="Email" onChange={(e) => setLoginData({ ...loginData, email: e.target.value })} />
            <Input placeholder="Senha" type="password" onChange={(e) => setLoginData({ ...loginData, password: e.target.value })} />
            <Button onClick={handleLogin}>Entrar</Button>
          </CardContent>
        </Card>
      ) : (
        <>
          <Card className="mb-4">
            <CardContent className="space-y-2">
              <h2 className="text-lg font-semibold">Olá, {user.name}!</h2>
              <Input
                placeholder="Escreva uma notícia ou anúncio de venda..."
                value={postText}
                onChange={(e) => setPostText(e.target.value)}
              />
              <Button onClick={handlePost}>Publicar</Button>
              <div className="pt-2">
                <Select onValueChange={setFilter} defaultValue="todos">
                  <SelectTrigger>
                    <SelectValue placeholder="Filtrar por tipo" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="todos">Todos</SelectItem>
                    <SelectItem value="noticia">Notícias</SelectItem>
                    <SelectItem value="venda">Vendas</SelectItem>
                  </SelectContent>
                </Select>
              </div>
            </CardContent>
          </Card>

          {filteredPosts.map((post) => (
            <Card key={post.id} className="mb-3">
              <CardContent>
                <p className="font-semibold">{post.user}</p>
                <p>{post.content}</p>
                <div className="mt-2">
                  <Input
                    placeholder="Escreva um comentário"
                    value={commentText[post.id] || ""}
                    onChange={(e) => setCommentText({ ...commentText, [post.id]: e.target.value })}
                  />
                  <Button className="mt-1" onClick={() => handleComment(post.id)}>
                    Comentar
                  </Button>
                  {post.comments.map((c, index) => (
                    <div key={index} className="mt-2 pl-2 border-l">
                      <p className="text-sm font-medium">{c.user}</p>
                      <p className="text-sm">{c.content}</p>
                    </div>
                  ))}
                </div>
              </CardContent>
            </Card>
          ))}
        </>
      )}
    </main>
  );
}
